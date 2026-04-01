# Claude Code Harness 工程化深度分析

> 来源：[../../../../journal/2026-04-01/kl16-claude-code-harness-analysis.md](../../../../journal/2026-04-01/kl16-claude-code-harness-analysis.md)
> 缩写：KH

## Atoms

| 编号  | 类型 | 内容                                                                                               | 原文定位                    |
| ----- | ---- | -------------------------------------------------------------------------------------------------- | --------------------------- |
| KH-01 | 事实 | Claude Code 是基于 Bun 运行时的 TypeScript 项目，包含 1900+ 文件和 51 万 + 行严格类型代码          | 2.1 Harness 架构概览        |
| KH-02 | 事实 | Claude Code 被定义为第三代 Autonomous Agent，其核心是本地运行时外壳包裹 LLM 提供工具与编排能力     | 2.1 Harness 架构概览        |
| KH-03 | 判断 | TAOR 循环的设计哲学是“运行时越笨，架构越稳定”，智能应完全下沉到模型而非堆砌脚手架               | 2.2 TAOR 循环               |
| KH-04 | 事实 | Orchestrator 组件仅含 50 行核心逻辑，负责驱动循环、执行工具及感知结果                           | 2.2 TAOR 循环               |
| KH-05 | 经验 | 工具层设计应提供 Shell 等原语让模型自由组合，而非预设上百个固定工具                             | 2.2 TAOR 循环               |
| KH-06 | 事实 | Context 管理采用三层防御：Auto-Compaction（50% 触发）、Sub-Agent 隔离、Prompt Cache 经济学      | 2.3 Context 管理            |
| KH-07 | 经验 | 函数命名如 `DANGEROUS_uncachedSystemPromptSection` 可作为文档警示该处修改会破坏缓存             | 2.3 Context 管理            |
| KH-08 | 判断 | 记忆系统的核心原则是“能从代码库重新推导的信息绝不应存储”，过期记忆被视为负债而非资产            | 2.4 记忆系统                |
| KH-09 | 事实 | 记忆系统分为六层：Managed Policy、Project CLAUDE.md、User Preferences、Auto-Memory、Session、Sub-Agent Memory | 2.4 记忆系统                |
| KH-10 | 事实 | 权限设计包含五级信任光谱：plan（只读）、default（询问）、acceptEdits（自动批准编辑）、dontAsk（白名单）、bypassPermissions | 2.5 权限设计                |
| KH-11 | 事实 | bashSecurity.ts 包含 23 项编号检查，涵盖 Zsh 扩展、Unicode 零宽字符注入及 IFS null-byte 注入等  | 2.5 权限设计                |
| KH-12 | 事实 | 底层 API 请求在 JS 层之下利用 Bun 原生 HTTP 栈替换占位符为哈希值以完成身份验证                  | 2.5 权限设计                |
| KH-13 | 事实 | Sub-Agent 采用主从模式，拥有独立进程、TAOR 循环及 Context 预算，内置 Explore、Plan、General-purpose 三种预设 | 2.6 多 Agent 编排           |
| KH-14 | 事实 | Agent Teams 通过共享文件系统协调，利用 Shared Task List、单播/广播消息及空闲通知钩子进行质量门控 | 2.6 多 Agent 编排           |
| KH-15 | 事实 | KAIROS 是未发布的 Always-On Agent 功能，包含夜间记忆蒸馏、每日日志、Webhook 订阅及 Cron 调度    | 2.7 KAIROS：Always-On Agent |
| KH-16 | 判断 | Agent 产品形态正从“召唤式”向“常驻后台、持续学习、主动感知”的下一代形态转变                      | 2.7 KAIROS：Always-On Agent |
| KH-17 | 事实 | Anti-Distillation 机制通过在 API 请求注入虚假工具定义来污染竞品训练数据                         | 2.8 安全与防御机制          |
| KH-18 | 事实 | Undercover Mode 强制隐藏内部代号与仓库名，且设计为单向门（可强制开启但无法强制关闭）            | 2.8 安全与防御机制          |
| KH-19 | 经验 | OpenClaw 的记忆分层目前偏“文件”存储，应借鉴 Claude Code 转向“索引”模式以优化效率                | 3.1 架构设计层面            |
| KH-20 | 步骤 | 实施 Context 压缩时，应在会话过长时自动摘要历史对话而非简单截断                                 | 3.2 具体可借鉴点            |
| KH-21 | 经验 | 应追踪哪些 Prompt 修改会破坏缓存，以此优化 Token 成本                                           | 3.2 具体可借鉴点            |
| KH-22 | 判断 | OpenClaw 相比 Claude Code 的独特优势在于多模型支持、知识棱镜集成及更灵活的 Skill 机制           | 3.3 OpenClaw 的独特优势     |
