# 用 ACP + Cursor Agent 替代 OpenClaw 原生编程——从文档分析到完整部署

> 日期：2026-03-27
> 项目：js-cursor-agent、openclaw
> 类型：调研分析 / 架构设计 / 功能实现
> 来源：Cursor Agent 对话

---

## 目录

1. [背景与动机](#1-背景与动机)
2. [分析过程：理解 ACP 体系](#2-分析过程理解-acp-体系)
3. [方案设计：整合 Cursor 为 ACP 后端](#3-方案设计整合-cursor-为-acp-后端)
4. [实现要点：四处改动](#4-实现要点四处改动)
5. [运行时行为分析](#5-运行时行为分析)
6. [后续演化](#6-后续演化)

---

## 1. 背景与动机

### 核心问题：OpenClaw 编程消耗 token 太贵

很多人反映 OpenClaw（龙虾）使用成本高。根本原因不是日常对话，而是 **龙虾在编程时大量消耗 token**——每次读文件、写代码、调工具都走 Chat 模型的上下文，token 累积很快。

### 解决思路：把编程外包给 Cursor

OpenClaw 从 3.24 版本起支持 **ACP（Agent Client Protocol）** 运行时。ACP 允许 Gateway 把编程任务「外包」给外部 harness（如 Codex、Claude Code、Cursor CLI 等），由外部工具自己的计费体系承担编码 token。OpenClaw 只负责编排和路由，大幅降低 OpenClaw 侧的 token 成本。

**之前已有 `js-cursor-agent` 插件**，实现了完整的 `AcpRuntime` 接口，但一直没有在配置里「开阀门」。这次的目标是：

1. 激活 ACP 运行时，让插件真正生效
2. 优化插件部署，让新装的 OpenClaw 快速配好
3. 理清两层限制重叠，去掉插件侧多余管控

---

## 2. 分析过程：理解 ACP 体系

### ACP 文档全景（`docs/cli/acp.md` + `docs/tools/acp-agents.md`）

ACP 在 OpenClaw 里分为 **两条线**，容易混淆：

| 线 | 文件 | 场景 |
|----|------|------|
| **IDE 桥** `openclaw acp` | `docs/cli/acp.md` | IDE（如 Zed）通过 stdio + ACP 接 Gateway |
| **运行时 + 外挂 harness** | `docs/tools/acp-agents.md` | Gateway 通过后端（如 acpx）驱动 Codex/Claude/Cursor 等 |

本次关注的是 **第二条线**。

### 三层概念厘清

| 概念 | 本质 | 类比 |
|------|------|------|
| **ACP 运行时** | Gateway 里的会话/策略/命令管道 | 外包岗位流程 |
| **acpx / cursor（后端 id）** | 注册到运行时的具体插件实现 | 某一家外包公司 |
| **ACP harness** | 被后端驱动的外部编码程序（Codex、Cursor CLI…） | 实际干活的「马」|

覆盖顺序：`bindings[].acp` → `agents.list[].runtime.acp` → 全局 `acp.*`

### 现有配置分析

分析 `d:\.openclaw\openclaw.json`，发现：
- `acp` 配置块：**不存在**（运行时大门未开）
- `acpx` 插件：**未安装**
- `js-cursor-agent`：**已安装、已启用**，但无 ACP 配置撑腰

结论：插件代码已经接好水管（实现了 `registerAcpRuntimeBackend({ id: "cursor" })`），只差配置里开阀门。

---

## 3. 方案设计：整合 Cursor 为 ACP 后端

### 关键决策

| 决策 | 选择 | 理由 |
|------|------|------|
| 后端 id | `cursor`（非 `acpx`） | 插件已用 `BACKEND_ID = 'cursor'` 注册 |
| 是否装 acpx | 不装 | 当前只需 Cursor，无需引入第二套后端 |
| 插件侧限制 | OpenClaw 模式下全部禁用（`maxSessions=0`, `idleTtlMinutes=0`） | Gateway 已有完整生命周期管理，避免重复和竞态 |
| 独立 CLI/MCP | 保留原有限制（4 并发、30 分钟回收） | 不走 Gateway 时仍需安全兜底 |
| 模型配置 | 加入插件 configSchema | 允许在 `openclaw.json` 里直接写 `model`，不必依赖环境变量 |

### 数据流

```text
聊天消息（飞书等）
    ↓
OpenClaw ACP 运行时（策略 + 会话 + 路由）
    ↓  acp.backend === "cursor"
js-cursor-agent 插件（CursorRuntime）
    ↓
ProcessManager → spawn agent.cmd acp（Cursor CLI）
    ↓  JSON-RPC over stdio
Cursor 的模型（不消耗 OpenClaw token）
```

---

## 4. 实现要点：四处改动

### 4.1 `openclaw.json`：开启 ACP

在 `gateway` 之前插入 `acp` 块：

```json
"acp": {
  "enabled": true,
  "backend": "cursor",
  "defaultAgent": "cursor",
  "allowedAgents": ["cursor"],
  "maxConcurrentSessions": 4,
  "stream": { "coalesceIdleMs": 300, "maxChunkChars": 1200 },
  "runtime": { "ttlMinutes": 30 }
}
```

同时为插件配齐：

```json
"js-cursor-agent": {
  "enabled": true,
  "config": {
    "command": "C:\\Users\\...\\agent.cmd",
    "model": "composer-1.5",
    "defaultMode": "agent",
    "permissionMode": "approve-all"
  }
}
```

### 4.2 `openclaw-plugin/openclaw.plugin.json`：规范元数据

参照 `acpx` 插件格式，新建 `openclaw.plugin.json`：
- `configSchema`：声明 `command`、`apiKey`、`authToken`、`endpoint`、`model`、`defaultMode`、`permissionMode` 等字段与类型约束
- `uiHints`：每个字段的 label + help 文案
- **移除**了 `idleTtlMinutes` 和 `maxConcurrentSessions`（OpenClaw 模式下不生效）

### 4.3 `openclaw-plugin/index.mjs`：三项增强

| 改动 | 说明 |
|------|------|
| **Gateway 模式禁用插件限制** | `overrides = { maxSessions: 0, idleTtlMinutes: 0 }` 硬编码 |
| **新增 `openclaw cursor setup`** | 一条命令自动 `config set` 所有 ACP 配置项；支持 `--dry-run` |
| **启动时 ACP 检测** | service start 时检查 `acp.enabled` / `acp.backend`，未配好则 warn + 告诉用户跑 `cursor setup` |
| **`model` 映射** | `pluginCfg.model` → `overrides.model` → `resolveAuthArgs` → `--model` |

### 4.4 `core/process-manager.js` + `core/config.js`：支持「不限制」

| 文件 | 改动 |
|------|------|
| `config.js` | `intOrDefault` 改为 `parsed >= 0`，让 `0` 穿透而非回退默认值 |
| `process-manager.js` | `maxSessions <= 0` 跳过并发检查；`idleTtlMinutes <= 0` 不启动 reaper |

### 改动文件汇总

| 文件 | 仓库 | 变更 |
|------|------|------|
| `d:\.openclaw\openclaw.json` | 配置 | 新增 `acp` 块 + 插件 config 补齐 |
| `openclaw-plugin/openclaw.plugin.json` | js-cursor-agent | 新建 |
| `openclaw-plugin/index.mjs` | js-cursor-agent | setup 命令 + ACP 检测 + model 映射 + 禁用插件限制 |
| `core/process-manager.js` | js-cursor-agent | 支持 0 = 不限制 |
| `core/config.js` | js-cursor-agent | `intOrDefault` 允许 0 |
| `TOOLS.md` | 工作区 | 新增 Cursor Agent 使用指南 |
| `MEMORY.md` | 工作区 | 重写编程工具偏好 |
| `AGENTS.md` | 工作区 | 补充 Cursor/ACP 交叉引用 |

---

## 5. 运行时行为分析

### 长时间任务

- 单轮上限取决于 Gateway `timeoutSeconds`（当前 4800 = ~80 分钟）
- JSON-RPC 层有 24 小时安全兜底
- 空闲回收由 Gateway `RuntimeCache` 管理（`acp.runtime.ttlMinutes`）

### 是否阻塞 main 通道

**不阻塞。** ACP 会话有独立 session key（`agent:main:acp:<uuid>`），与 main agent 完全隔离：
- 不同 session key → 不同 actor queue → 完全并行
- 共享资源仅 `maxConcurrentSessions`（进程数），满了新 spawn 报错但不影响 main

### 监控手段

| 层级 | 手段 |
|------|------|
| 聊天 | `/acp status`、`/acp sessions`、`/acp doctor` |
| CLI | `openclaw cursor doctor`、`openclaw cursor sessions` |
| 日志 | Gateway 每轮打 `acp-dispatch: session=... outcome=... latencyMs=... queueDepth=...` |

### 可用模型（本机 `agent.cmd --list-models`）

包括但不限于：`auto`、`composer-1.5`(current)、`claude-4.6-opus-high-thinking`(default)、`claude-4.6-sonnet-medium-thinking`、`gpt-5.4-medium`、`gemini-3.1-pro`、`gemini-3-flash`、`kimi-k2.5` 等 80+ 个模型。完整列表随 Cursor CLI 版本和账号套餐变化，需 `agent.cmd --list-models` 实时查看。

---

## 6. 后续演化

### 近期

- **重启 Gateway 验证**：`/acp doctor` → `/acp spawn cursor --cwd <仓库>` → 看回复
- **模型选择调优**：当前默认 `composer-1.5`，可尝试 `claude-4.6-sonnet-medium-thinking` 或 `auto`
- **飞书线程绑定**：待确认飞书插件是否支持 `threadBindings`；若不支持则每次显式 `--cwd`

### 中期

- **`openclaw cursor setup --dry-run`** 测试通过后可写进插件 README
- **npm 发包**：将 `js-cursor-agent` 发到 npm，让其他用户 `openclaw plugins install js-cursor-agent` 即可
- **多模型切换**：考虑支持 `/acp model <id>` 在不重启的情况下切换 Cursor 模型

### 长期

- **方案 A（多后端并存）**：如需 Codex/Claude Code，可装 acpx 做第二后端
- **方案 B（单后端多 harness）**：扩展 js-cursor-agent 支持多个 harness
- **成本量化**：对比 ACP 前后 OpenClaw 的 token 消耗，验证降本效果

---

## 核心洞察

> **编程是龙虾 token 消耗的大头。** 通过 ACP 把编码任务交给 Cursor CLI（走 Cursor 自己的计费），OpenClaw 只做编排和路由，是**从成本角度玩龙虾必须要做的一件事**。这不是可选优化，是成本结构性改变。

---
