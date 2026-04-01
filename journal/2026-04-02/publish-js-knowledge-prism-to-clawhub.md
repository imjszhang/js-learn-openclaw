# 将 js-knowledge-prism 发布到 ClawHub

> 日期：2026-04-02
> 项目：js-knowledge-prism、openclaw
> 类型：调研分析 / 功能实现
> 来源：Cursor Agent 对话

---

## 目录

1. [背景与动机](#1-背景与动机)
2. [分析过程：ClawHub 发布机制调研](#2-分析过程clawhub-发布机制调研)
3. [方案设计：Skill 还是 Plugin？](#3-方案设计skill-还是-plugin)
4. [实现要点：发布执行](#4-实现要点发布执行)
5. [验证与测试](#5-验证与测试)
6. [后续演化](#6-后续演化)

---

## 1. 背景与动机

js-knowledge-prism 已经作为 OpenClaw 插件在本地稳定运行，具备完整的 AI 工具注册、CLI 子命令、Web UI、Cron 定时任务等能力。希望将其发布到 ClawHub 公共注册表，让其他 OpenClaw 用户也能一键安装使用。

核心问题：
- ClawHub 的发布机制是什么？流程如何？
- 当前项目适合按 Skill 还是 Plugin 发布？
- 具体怎么操作？

## 2. 分析过程：ClawHub 发布机制调研

通过阅读 `openclaw/docs` 中的 ClawHub 相关文档，梳理出以下关键信息：

### ClawHub 是什么

- OpenClaw 的公共注册表，托管 **Skills 和 Plugins**
- 网站：https://clawhub.ai
- 使用嵌入向量的语义搜索（不仅是关键词）
- 支持 semver 版本管理、标签、变更日志

### 两种发布形态

| 形态 | 本质 | 发布方式 | 安装方式 |
| ---- | ---- | ---- | ---- |
| **Skill** | 含 `SKILL.md` 的纯指令目录，教 Agent 如何使用工具 | `clawhub publish <path>` | `clawhub install <slug>` 或 `openclaw skills install <slug>` |
| **Plugin** | 含 `openclaw.plugin.json` 的可执行包，注册工具/CLI/路由等 | 发到 ClawHub 或 npm | `openclaw plugins install <pkg>` 或 `openclaw plugins install clawhub:<pkg>` |

### 发布前置条件

- 安装 ClawHub CLI：`npm i -g clawhub`
- 登录：`clawhub login --token <token>` 或浏览器流程
- GitHub 账号注册需满约一周才能发布（防滥用）

### 关键文档来源

| 文档 | 内容 |
| ---- | ---- |
| `tools/clawhub.md` | ClawHub 完整指南：CLI 命令、参数、工作流 |
| `tools/creating-skills.md` | Skill 创建步骤和 SKILL.md 格式 |
| `plugins/building-plugins.md` | Plugin 开发快速入门 |
| `plugins/community.md` | 社区插件列表和提交要求 |

## 3. 方案设计：Skill 还是 Plugin？

### 项目能力盘点

| 能力 | Skill 能做 | Plugin 能做 | 项目具备 |
| ---- | ---- | ---- | ---- |
| 注册 18+ AI 工具（`knowledge_prism_*`） | 否 | 是 | **是** |
| 注册 CLI 子命令（`openclaw prism ...`） | 否 | 是 | **是** |
| 注册 HTTP 路由（Web UI） | 否 | 是 | **是** |
| Cron 定时任务集成 | 否 | 是 | **是** |
| `openclaw.plugin.json` 配置清单 | 否 | 是 | **是** |
| 可执行入口（`index.mjs`） | 否 | 是 | **是** |
| 附带子 Skills | 可能 | 是 | **是**（3 个） |

### 关键决策

| 决策 | 选择 | 理由 |
| ---- | ---- | ---- |
| 发布形态 | **Plugin** | 项目核心价值在工具注册、CLI、HTTP 路由、Cron——全是 Plugin 能力，Skill 做不到 |
| 根目录 SKILL.md 定位 | 插件随附文档 | 它是 Agent 使用指引，不是独立 Skill；随 Plugin 一起分发 |
| 发布渠道 | **ClawHub**（而非 npm） | ClawHub 是 OpenClaw 原生渠道，安装优先级高于 npm |

### 项目关键结构

```
js-knowledge-prism/
├── SKILL.md                    ← Agent 使用文档（随 Plugin 分发）
├── package.json                ← npm 包定义（bin: js-knowledge-prism CLI）
├── openclaw-plugin/
│   ├── openclaw.plugin.json    ← Plugin 清单（configSchema + uiHints）
│   ├── package.json            ← ESM 模块（openclaw.extensions 入口）
│   ├── index.mjs               ← 插件逻辑：18 AI 工具 + CLI + HTTP 路由
│   └── skills/                 ← 3 个附带 Skill
│       ├── prism-processor/
│       ├── prism-template-author/
│       └── prism-rewrite-author/
├── lib/                        ← 核心处理逻辑
├── bin/                        ← CLI 入口
└── templates/                  ← 模板文件
```

## 4. 实现要点：发布执行

### 步骤 1：安装 ClawHub CLI

```bash
npm i -g clawhub
clawhub --cli-version
# → 0.9.0
```

### 步骤 2：使用 Token 登录

Token 存放在项目 `.env` 文件中（`CLAWHUB_CLI_TOKEN=clh_...`）。

```bash
clawhub login --token "<token>" --no-browser
# → ✓ OK. Logged in as @imjszhang.
```

### 步骤 3：发布

```bash
clawhub publish "D:/github/my/js-knowledge-prism" \
  --slug js-knowledge-prism \
  --name "JS Knowledge Prism" \
  --version 1.8.0 \
  --tags latest \
  --no-input
# → ✓ OK. Published js-knowledge-prism@1.8.0 (k974188snajtpmvkhrscfd1w2h841feq)
```

注意事项：
- `clawhub publish .` 用相对路径 `.` 时报 "SKILL.md required"，但用完整绝对路径 `D:/github/my/js-knowledge-prism` 成功——疑似 CLI 在 Windows 下对相对路径的解析有问题
- `--slug` 使用 `js-knowledge-prism`（与 package.json 的 name 一致）
- `--version 1.8.0` 取自 SKILL.md frontmatter 中的 version 字段

### 步骤 4：验证

发布后在浏览器确认页面：

```
https://clawhub.ai/imjszhang/js-knowledge-prism
```

页面正常显示：
- 标题：**JS Knowledge Prism** v1.8.0
- 作者：JS · @imjszhang
- 许可证：MIT-0
- 提供 Download zip 按钮

## 5. 验证与测试

| 验证项 | 结果 |
| ---- | ---- |
| `clawhub publish` 命令 | 成功，返回版本 hash |
| ClawHub 页面 | 正常显示，`clawhub.ai/imjszhang/js-knowledge-prism` |
| 安全扫描 | ClawHub 自动安全扫描标记为 "suspicious patterns detected"，原因是 SKILL.md 中包含读取环境变量和 OpenClaw 配置的指令 |
| VirusTotal | Pending 状态 |
| 语义搜索 | 新发布条目尚未被索引到搜索结果中（向量搜索索引有延迟） |

### 安全扫描说明

ClawHub 的静态分析检测到 SKILL.md 中包含探测环境变量（`env | grep OPENCLAW_`）和读取配置文件的指令，自动标记为可疑。这是预期内的行为——因为插件确实需要检测运行环境来判断 Plugin Mode vs Standalone Mode。大概率 VirusTotal 扫描通过后会自动解除。

## 6. 后续演化

- **安全扫描跟进**：观察 VirusTotal 扫描结果，如持续被标记，考虑精简 SKILL.md 中环境探测部分的描述
- **版本更新流程**：后续版本更新可用 `clawhub publish --version <新版本>` 或 `clawhub sync` 批量同步
- **安装命令文档化**：在项目 README 中补充 ClawHub 安装方式：
  ```bash
  clawhub install js-knowledge-prism
  # 或
  openclaw plugins install clawhub:js-knowledge-prism
  ```
- **社区插件列表**：可选向 OpenClaw 文档提 PR，将 js-knowledge-prism 加入 Community Plugins 页面（需满足公开 GitHub 仓库、文档齐全等条件）

---
