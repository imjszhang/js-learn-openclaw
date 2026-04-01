# Claude Code Harness 工程化深度分析

> 日期：2026-04-01
> 项目：OpenClaw / js-learn-openclaw
> 类型：调研分析
> 来源：Founder Park 文章学习

---

## 1. 背景与动机

Claude Code 源码泄露事件引发了对 Agent 工程化架构的广泛讨论。Founder Park 的这篇文章深入解析了 Claude Code 的 Harness 设计，核心论点是：**真正难的不是模型，而是 Harness**。

这篇文章与 KL15「当 Claude Code 被扒光，我看到了 OpenClaw 的未来」形成互补——KL15 侧重 Skill 映射，本文侧重 Harness 的工程细节。

**学习目标**：
- 理解 Claude Code 的 TAOR 循环设计哲学
- 分析 Context 管理、记忆系统、权限设计的工程实现
- 提炼对 OpenClaw 架构设计的启示

---

## 2. 核心发现

### 2.1 Harness 架构概览

Claude Code 是一个基于 Bun 运行时的 TypeScript 项目：
- **规模**：1900+ 文件，51万+ 行严格类型代码
- **运行时**：Bun + React + Ink（终端 UI）
- **核心**：本地运行时外壳，包裹 LLM 提供工具、记忆、编排能力

**架构定位**：第三代 Autonomous Agent（模型控制循环，运行时只是执行器）

### 2.2 TAOR 循环："运行时越笨，架构越稳定"

| 组件 | 职责 | 设计哲学 |
|------|------|----------|
| Orchestrator | 驱动循环、执行工具、感知结果 | 极简，只有 50 行核心逻辑 |
| LLM | 所有推理、决策、停止判断 | 智能完全下沉到模型 |
| 工具层 | Read/Write/Execute/Connect 四种原语 | 不给 100 个工具，给 1 个 shell 让模型组合 |

**关键洞察**：随着模型变强，脚手架应该变薄，而不是变厚。

### 2.3 Context 管理：稀缺资源经济学

Claude Code 将 Context Window 视为需要主动管理的稀缺资源，三层防御体系：

| 层级 | 机制 | 触发条件 |
|------|------|----------|
| Auto-Compaction | LLM 摘要替换原始对话 | Context 使用达 50% |
| Sub-Agent 隔离 | 重型任务卸载给独立子 Agent | 探索/研究类任务 |
| Prompt Cache 经济学 | 追踪 14 个 cache-break 向量 | 实时监控 |

**细节**：`DANGEROUS_uncachedSystemPromptSection()` 函数命名本身就是一种文档——提示这里加内容要小心，会破坏缓存。

### 2.4 记忆系统：索引而非存储

**核心原则**：能从代码库重新推导的信息，绝不应存储。

**六层结构**（会话启动时按层加载）：
1. Managed Policy（组织级策略）
2. Project CLAUDE.md（项目配置）
3. User Preferences（用户偏好）
4. Auto-Memory（自动学习模式）
5. Session（会话上下文）
6. Sub-Agent Memory（子 Agent 记忆）

**自编辑能力**：主动重写、去重、剪除矛盾信息，过期记忆被视为"负债"而非资产。

### 2.5 权限设计：可组合的信任光谱

| 级别 | 能力 | 适用场景 |
|------|------|----------|
| plan | 只读，完全不能写入 | 最高受限环境 |
| default | 编辑和 shell 前询问 | 标准模式 |
| acceptEdits | 自动批准文件编辑 | 中等信任 |
| dontAsk | 自动批准白名单操作 | 高信任 |
| bypassPermissions | 跳过所有检查 | 仅限托管组织 |

**安全检查**：bashSecurity.ts 包含 23 项编号检查，包括 Zsh equals expansion、Unicode 零宽字符注入、IFS null-byte 注入等。

**底层机制**：API 请求在 JS 层之下做身份验证（Bun 原生 HTTP 栈替换 cch=00000 占位符为哈希值）。

### 2.6 多 Agent 编排

**Sub-Agent（主从模式）**：
- 独立进程、独立 TAOR 循环、独立 Context 预算
- 内置三种预设：Explore（Haiku，只读）、Plan（继承模型，只读）、General-purpose（全套工具）
- 支持前台/后台两种执行模式

**Agent Teams（实验性）**：
- 独立 Claude Code 实例通过共享文件系统协调
- 机制：Shared Task List、单播 Message、Broadcast、Automatic Idle Notification
- 质量门控：TeammateIdle Hook、TaskCompleted Hook

### 2.7 KAIROS：Always-On Agent

泄露代码显示未发布的 KAIROS 功能：
- /dream 技能：夜间记忆蒸馏
- 每日 append-only 日志
- GitHub Webhook 订阅
- 后台 Daemon 工作进程
- 每 5 分钟 Cron 调度刷新

**产品形态转变**：从"召唤式 Agent"到"常驻后台、持续学习、主动感知"的下一代形态。

### 2.8 安全与防御机制

| 机制 | 实现 | 目的 |
|------|------|------|
| Anti-Distillation | API 请求携带 anti_distillation 参数，注入虚假工具定义 | 污染竞品训练数据 |
| Undercover Mode | 强制不提及内部代号、Slack 频道、仓库名 | 保护商业机密 |

**Undercover Mode 特性**：单向门，可强制开启，无法强制关闭（`There is NO force-OFF`）。

---

## 3. 对 OpenClaw 的启示

### 3.1 架构设计层面

| Claude Code | OpenClaw 现状 | 差距/机会 |
|-------------|---------------|-----------|
| TAOR 循环 | Agent Turn + Tool Call 循环 | OpenClaw 也有类似结构，但 Orchestrator 可以更简单 |
| Sub-Agent 隔离 | sessions_spawn + 隔离会话 | 已有基础，但 Context 压缩机制可加强 |
| 四层记忆 | SOUL/USER/MEMORY/TOOLS + Memory-Core | OpenClaw 的记忆分层更偏"文件"而非"索引"，可优化 |
| 权限光谱 | 工具级权限控制 | OpenClaw 可通过配置实现类似分级 |

### 3.2 具体可借鉴点

1. **Context 压缩**：当会话过长时，自动摘要历史对话而非简单截断
2. **Sub-Agent 专用记忆**：每个子 Agent 有自己的 MEMORY.md，类似 OpenClaw 的 AGENTS.md 但粒度更细
3. **Prompt 缓存意识**：追踪哪些修改会破坏缓存，优化 Token 成本
4. **权限渐进**：从"什么都问"到"自动批准"的分级信任模型

### 3.3 OpenClaw 的独特优势

- **多模型支持**：Claude Code 绑定 Anthropic，OpenClaw 可切换不同模型
- **知识棱镜集成**：Claude Code 没有类似的知识处理管道
- **Cron 原生支持**：KAIROS 的常驻模式在 OpenClaw 中通过 Cron 已实现
- **技能生态**：OpenClaw 的 Skill 机制比 Claude Code 的 MCP 更灵活

---

## 4. 后续演化

### 近期可跟进
- [ ] 对比 OpenClaw 与 Claude Code 的 Harness 差异，形成系统分析文章
- [ ] 评估 Context 压缩功能在 OpenClaw 中的可行性
- [ ] 研究 Sub-Agent 专用记忆的实现方式

### 长期方向
- 关注 KAIROS 正式发布后的产品形态演进
- 跟踪 Anthropic 对 OpenCode 等第三方工具的法律态度变化
- 思考"Harness Engineering"作为方法论的系统化表述

---

## 参考链接

- 原文：https://mp.weixin.qq.com/s/2NUlZtRMbNHpBvgAe3__Qg
- 相关文章：KL15「当 Claude Code 被扒光，我看到了 OpenClaw 的未来」
- 知识库记录：aek7ablbq5phae8
