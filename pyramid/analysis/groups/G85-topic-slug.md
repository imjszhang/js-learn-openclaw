# G85: Claude Code 的 Harness 工程化揭示了"极简 Orchestrator+ 原语工具层 + 分层记忆防御"的第三代 Agent 架构范式

> 第三代 Autonomous Agent 的核心在于将智能完全下沉到模型，通过极简的运行时外壳、原语化的工具组合及经济学的上下文管理，构建稳定且可进化的自主系统。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| KH-01 | kl16-claude-code-harness-analysis | Claude Code 是基于 Bun 运行时的 TypeScript 项目，包含 1900+ 文件和 51 万 + 行严格类型代码 |
| KH-02 | kl16-claude-code-harness-analysis | Claude Code 被定义为第三代 Autonomous Agent，其核心是本地运行时外壳包裹 LLM 提供工具与编排能力 |
| KH-03 | kl16-claude-code-harness-analysis | TAOR 循环的设计哲学是“运行时越笨，架构越稳定”，智能应完全下沉到模型而非堆砌脚手架 |
| KH-04 | kl16-claude-code-harness-analysis | Orchestrator 组件仅含 50 行核心逻辑，负责驱动循环、执行工具及感知结果 |
| KH-05 | kl16-claude-code-harness-analysis | 工具层设计应提供 Shell 等原语让模型自由组合，而非预设上百个固定工具 |
| KH-06 | kl16-claude-code-harness-analysis | Context 管理采用三层防御：Auto-Compaction（50% 触发）、Sub-Agent 隔离、Prompt Cache 经济学 |
| KH-07 | kl16-claude-code-harness-analysis | 函数命名如 `DANGEROUS_uncachedSystemPromptSection` 可作为文档警示该处修改会破坏缓存 |
| KH-08 | kl16-claude-code-harness-analysis | 记忆系统的核心原则是“能从代码库重新推导的信息绝不应存储”，过期记忆被视为负债而非资产 |
| KH-09 | kl16-claude-code-harness-analysis | 记忆系统分为六层：Managed Policy、Project CLAUDE.md、User Preferences、Auto-Memory、Session、Sub-Agent Memory |
| KH-10 | kl16-claude-code-harness-analysis | 权限设计包含五级信任光谱：plan（只读）、default（询问）、acceptEdits（自动批准编辑）、dontAsk（白名单）、bypassPermissions |
| KH-11 | kl16-claude-code-harness-analysis | bashSecurity.ts 包含 23 项编号检查，涵盖 Zsh 扩展、Unicode 零宽字符注入及 IFS null-byte 注入等 |
| KH-12 | kl16-claude-code-harness-analysis | 底层 API 请求在 JS 层之下利用 Bun 原生 HTTP 栈替换占位符为哈希值以完成身份验证 |
| KH-13 | kl16-claude-code-harness-analysis | Sub-Agent 采用主从模式，拥有独立进程、TAOR 循环及 Context 预算，内置 Explore、Plan、General-purpose 三种预设 |
| KH-14 | kl16-claude-code-harness-analysis | Agent Teams 通过共享文件系统协调，利用 Shared Task List、单播/广播消息及空闲通知钩子进行质量门控 |
| KH-15 | kl16-claude-code-harness-analysis | KAIROS 是未发布的 Always-On Agent 功能，包含夜间记忆蒸馏、每日日志、Webhook 订阅及 Cron 调度 |
| KH-16 | kl16-claude-code-harness-analysis | Agent 产品形态正从“召唤式”向“常驻后台、持续学习、主动感知”的下一代形态转变 |
| KH-17 | kl16-claude-code-harness-analysis | Anti-Distillation 机制通过在 API 请求注入虚假工具定义来污染竞品训练数据 |
| KH-18 | kl16-claude-code-harness-analysis | Undercover Mode 强制隐藏内部代号与仓库名，且设计为单向门（可强制开启但无法强制关闭） |
| KH-19 | kl16-claude-code-harness-analysis | OpenClaw 的记忆分层目前偏“文件”存储，应借鉴 Claude Code 转向“索引”模式以优化效率 |
| KH-20 | kl16-claude-code-harness-analysis | 实施 Context 压缩时，应在会话过长时自动摘要历史对话而非简单截断 |
| KH-21 | kl16-claude-code-harness-analysis | 应追踪哪些 Prompt 修改会破坏缓存，以此优化 Token 成本 |
| KH-22 | kl16-claude-code-harness-analysis | OpenClaw 相比 Claude Code 的独特优势在于多模型支持、知识棱镜集成及更灵活的 Skill 机制 |

## 组内逻辑顺序

遵循"架构定义 (KH-01~05) -> 核心机制 (KH-06~12) -> 高级形态 (KH-13~18) -> 对比与演进 (KH-19~22)"的结构顺序。首先确立第三代 Agent 的极简主义设计哲学，接着深入解析上下文、记忆与安全的具体实现，然后展示多 Agent 协作与未来形态，最后落脚于对 OpenClaw 的改进启示。
