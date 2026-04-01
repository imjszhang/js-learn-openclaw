# 从 Claude Code 源码学习 OpenClaw Harness Engineering 配置

> 来源：[../../../../journal/2026-04-01/kl15-learn-from-claude-code.md](../../../../journal/2026-04-01/kl15-learn-from-claude-code.md)
> 缩写：KF

## Atoms

| 编号  | 类型 | 内容                                                                                               | 原文定位                    |
| ----- | ---- | -------------------------------------------------------------------------------------------------- | --------------------------- |
| KF-01 | 事实 | 2026-03-31 因 npm 配置疏忽导致 Claude Code 51.2 万行 TypeScript 源码泄露，引发全网 60k 人 Fork      | 背景与动机                  |
| KF-02 | 判断 | 社区从泄露源码中提炼的 8 套 Agent 设计模式与 OpenClaw 的 Harness Engineering 架构高度同构            | 背景与动机                  |
| KF-03 | 事实 | Claude Code 通过启动时读取主分支、当前分支、最近提交及 CLAUDE.md 实现实时仓库上下文加载              | 关键素材提取                |
| KF-04 | 步骤 | OpenClaw 对应实时上下文加载的配置方式为预加载 SOUL.md、USER.md 和 AGENTS.md 文件                   | 关键素材提取                |
| KF-05 | 事实 | Claude Code 采用静态部分全局缓存、动态部分按需更新的激进 Prompt 缓存复用策略                       | 关键素材提取                |
| KF-06 | 步骤 | OpenClaw 实现缓存复用的方法是配置 memory_search 进行混合检索并结合嵌入缓存                         | 关键素材提取                |
| KF-07 | 事实 | Claude Code 使用 Grep/Glob/LSP 等专用工具链，其权限控制机制优于直接使用 Bash                       | 关键素材提取                |
| KF-08 | 事实 | OpenClaw 通过 Bitable 技能支持 27 种以上字段类型以构建专用工具链                                   | 关键素材提取                |
| KF-09 | 事实 | Claude Code 通过文件去重、磁盘写入和自动截断摘要来解决上下文膨胀问题                               | 关键素材提取                |
| KF-10 | 步骤 | OpenClaw 利用 knowledge_prism 将 Atom 收敛为 Group 再合成 Synthesis 以压缩上下文                   | 关键素材提取                |
| KF-11 | 事实 | Claude Code 使用 Markdown 格式存储会话状态、任务和工作流以实现结构化会话记忆                       | 关键素材提取                |
| KF-12 | 步骤 | OpenClaw 通过 MEMORY.md 根文件与 memory/YYYY-MM-DD.md 分层文件实现结构化会话记忆                   | 关键素材提取                |
| KF-13 | 事实 | Claude Code 支持 Fork 和子 Agent 并行，子 Agent 可复用父级缓存并感知可变状态                       | 关键素材提取                |
| KF-14 | 步骤 | OpenClaw 通过 sessions_spawn 创建 ACP 子代理并结合 subagents 进行管理以实现并行架构              | 关键素材提取                |
| KF-15 | 经验 | Coordinator Orchestrator 模式要求禁止懒委托，必须消化研究结果后给出精确指令                        | 关键素材提取                |
| KF-16 | 经验 | Task Concurrency Patterns 原则是只读操作并行、写操作串行，并使用 AsyncLocalStorage 进行隔离        | 关键素材提取                |
| KF-17 | 经验 | Adversarial Verification 的核心目标不是确认正确性，而是主动尝试打破现有结论                        | 关键素材提取                |
| KF-18 | 经验 | Self-Rationalization Guard 用于识别并阻止 AI 产生的自我合理化行为                                  | 关键素材提取                |
| KF-19 | 经验 | Worker Prompt Craft 要求指令必须自包含，禁止使用“修复我们讨论的 bug"这类依赖上下文的模糊表述       | 关键素材提取                |
| KF-20 | 事实 | Memory Type System 将记忆划分为 user、feedback、project、reference 四类                            | 关键素材提取                |
| KF-21 | 步骤 | OpenClaw 通过区分 MEMORY.md、memory/*.md 和 knowledge_prism 来实现四类记忆类型系统               | 关键素材提取                |
| KF-22 | 经验 | Smart Memory Guard 需实施漂移防护、膨胀检查和写入过滤机制                                        | 关键素材提取                |
| KF-23 | 步骤 | OpenClaw 通过在 memory_search 中应用时间衰减算法和嵌入缓存来实现智能记忆防护                     | 关键素材提取                |
| KF-24 | 经验 | Lightweight Explorer 模式适用于只读、快速、低成本的并行搜索场景                                  | 关键素材提取                |
| KF-25 | 步骤 | OpenClaw 通过并发执行 web_search 和 knowledge_search 来实现轻量级探索者模式                      | 关键素材提取                |
| KF-26 | 事实 | Claude Code 内置 Buddy System 电子宠物系统，用于设备配对和通知                                     | 关键素材提取                |
| KF-27 | 事实 | KAIROS 是 Claude Code 中支持 7x24 小时自主运行的架构组件                                           | 关键素材提取                |
| KF-28 | 步骤 | OpenClaw 通过配置 cron 定时任务和 HEARTBEAT.md 文件来模拟 KAIROS 的自主运行架构                  | 关键素材提取                |
| KF-29 | 判断 | Claude Code 的核心优势在于本地化工程优化而非模型本身，证明工程架构价值大于单一产品                 | 方案设计：KL15 框架         |
| KF-30 | 判断 | 分布式 Skill 生态系统的价值高于巨型单体系统                                                        | 方案设计：KL15 框架         |
