# G84: OpenClaw 的 Harness Engineering 架构与 Claude Code 源码泄露揭示的设计模式高度同构，证明工程优化价值超越单一模型

> 通过对比 Claude Code 泄露源码与 OpenClaw 架构，证实了本地化工程优化、结构化记忆系统及并行代理模式是构建高效 AI 助手的核心，而非依赖模型本身的智力。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| KF-01 | kl15-learn-from-claude-code | 因 npm 配置疏忽导致 Claude Code 51.2 万行 TypeScript 源码泄露，引发全网 60k 人 Fork |
| KF-02 | kl15-learn-from-claude-code | 社区从泄露源码中提炼的 8 套 Agent 设计模式与 OpenClaw 的 Harness Engineering 架构高度同构 |
| KF-03 | kl15-learn-from-claude-code | Claude Code 通过启动时读取主分支、当前分支、最近提交及 CLAUDE.md 实现实时仓库上下文加载 |
| KF-04 | kl15-learn-from-claude-code | OpenClaw 对应实时上下文加载的配置方式为预加载 SOUL.md、USER.md 和 AGENTS.md 文件 |
| KF-05 | kl15-learn-from-claude-code | Claude Code 采用静态部分全局缓存、动态部分按需更新的激进 Prompt 缓存复用策略 |
| KF-06 | kl15-learn-from-claude-code | OpenClaw 实现缓存复用的方法是配置 memory_search 进行混合检索并结合嵌入缓存 |
| KF-07 | kl15-learn-from-claude-code | Claude Code 使用 Grep/Glob/LSP 等专用工具链，其权限控制机制优于直接使用 Bash |
| KF-08 | kl15-learn-from-claude-code | OpenClaw 通过 Bitable 技能支持 27 种以上字段类型以构建专用工具链 |
| KF-09 | kl15-learn-from-claude-code | Claude Code 通过文件去重、磁盘写入和自动截断摘要来解决上下文膨胀问题 |
| KF-10 | kl15-learn-from-claude-code | OpenClaw 利用 knowledge_prism 将 Atom 收敛为 Group 再合成 Synthesis 以压缩上下文 |
| KF-11 | kl15-learn-from-claude-code | Claude Code 使用 Markdown 格式存储会话状态、任务和工作流以实现结构化会话记忆 |
| KF-12 | kl15-learn-from-claude-code | OpenClaw 通过 MEMORY.md 根文件与 memory/YYYY-MM-DD.md 分层文件实现结构化会话记忆 |
| KF-13 | kl15-learn-from-claude-code | Claude Code 支持 Fork 和子 Agent 并行，子 Agent 可复用父级缓存并感知可变状态 |
| KF-14 | kl15-learn-from-claude-code | OpenClaw 通过 sessions_spawn 创建 ACP 子代理并结合 subagents 进行管理以实现并行架构 |
| KF-15 | kl15-learn-from-claude-code | Coordinator Orchestrator 模式要求禁止懒委托，必须消化研究结果后给出精确指令 |
| KF-16 | kl15-learn-from-claude-code | Task Concurrency Patterns 原则是只读操作并行、写操作串行，并使用 AsyncLocalStorage 进行隔离 |
| KF-17 | kl15-learn-from-claude-code | Adversarial Verification 的核心目标不是确认正确性，而是主动尝试打破现有结论 |
| KF-18 | kl15-learn-from-claude-code | Self-Rationalization Guard 用于识别并阻止 AI 产生的自我合理化行为 |
| KF-19 | kl15-learn-from-claude-code | Worker Prompt Craft 要求指令必须自包含，禁止使用“修复我们讨论的 bug"这类依赖上下文的模糊表述 |
| KF-20 | kl15-learn-from-claude-code | Memory Type System 将记忆划分为 user、feedback、project、reference 四类 |
| KF-21 | kl15-learn-from-claude-code | OpenClaw 通过区分 MEMORY.md、memory/*.md 和 knowledge_prism 来实现四类记忆类型系统 |
| KF-22 | kl15-learn-from-claude-code | Smart Memory Guard 需实施漂移防护、膨胀检查和写入过滤机制 |
| KF-23 | kl15-learn-from-claude-code | OpenClaw 通过在 memory_search 中应用时间衰减算法和嵌入缓存来实现智能记忆防护 |
| KF-24 | kl15-learn-from-claude-code | Lightweight Explorer 模式适用于只读、快速、低成本的并行搜索场景 |
| KF-25 | kl15-learn-from-claude-code | OpenClaw 通过并发执行 web_search 和 knowledge_search 来实现轻量级探索者模式 |
| KF-26 | kl15-learn-from-claude-code | Claude Code 内置 Buddy System 电子宠物系统，用于设备配对和通知 |
| KF-27 | kl15-learn-from-claude-code | KAIROS 是 Claude Code 中支持 7x24 小时自主运行的架构组件 |
| KF-28 | kl15-learn-from-claude-code | OpenClaw 通过配置 cron 定时任务和 HEARTBEAT.md 文件来模拟 KAIROS 的自主运行架构 |
| KF-29 | kl15-learn-from-claude-code | Claude Code 的核心优势在于本地化工程优化而非模型本身，证明工程架构价值大于单一产品 |
| KF-30 | kl15-learn-from-claude-code | 分布式 Skill 生态系统的价值高于巨型单体系统 |

## 组内逻辑顺序

本组 atoms 遵循“事件背景 -> 架构同构对比 -> 核心机制详解（上下文/缓存/工具/记忆） -> 高级模式（并行/验证/防护） -> 自主运行与价值结论”的逻辑顺序。首先描述源码泄露事件及架构同构发现，接着逐一对比上下文加载、缓存策略、工具链和记忆系统的具体实现，然后深入探讨并行任务、对抗性验证及防自我合理化等高级工程模式，最后以自主运行架构（KAIROS/Cron）及工程价值大于模型价值的结论收尾。
