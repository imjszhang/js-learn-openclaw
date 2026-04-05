# G90: 自动化演进的本质是决策权从人类向 AI 的逐步转移，需依据任务属性匹配四个委托层级

> 自动化的核心不在于定时执行，而在于根据任务的风险与频率，将决策权从 L1 手动控制逐步移交至 L4 事件触发，实现从操作员到监管者的心态转变。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| DL-01 | kl20-four-levels-of-delegation | 自动化的本质不是设置定时任务，而是逐步转移决策权的过程 |
| DL-02 | kl20-four-levels-of-delegation | L1 手动层级中人类负责检查、决策和下指令，AI 仅负责执行，委托程度为 0% |
| DL-03 | kl20-four-levels-of-delegation | L2 Cron 层级中人类设定精确时间点，AI 到点执行，委托程度为 30% |
| DL-04 | kl20-four-levels-of-delegation | L3 Heartbeat 层级中人类设定检查周期，AI 自行判断是否需要行动，委托程度为 60% |
| DL-05 | kl20-four-levels-of-delegation | L4 Hooks 层级中人类设定触发规则，AI 自动响应事件无需判断，委托程度为 90% |
| DL-06 | kl20-four-levels-of-delegation | L2 Cron 适合必须精确时间的任务（如日报、定时备份），但无法判断任务当天的必要性 |
| DL-07 | kl20-four-levels-of-delegation | L3 Heartbeat 的核心机制是返回"HEARTBEAT_OK"表示无事发生，适合批量检查和监控类任务以实现降噪 |
| DL-08 | kl20-four-levels-of-delegation | L4 Hooks 适合处理生命周期事件和审计日志，通过事件触发实现即时响应 |
| DL-09 | kl20-four-levels-of-delegation | 敏感操作（如 API key 修改、权限变更）和创意决策应保留在 L1 手动层级 |
| DL-10 | kl20-four-levels-of-delegation | 知识库自动处理、链接收集及社交媒体监控等周期性任务应配置为 L2 Cron 层级 |
| DL-11 | kl20-four-levels-of-delegation | 记忆维护任务应在 L3 Heartbeat 层级执行，包括回顾对话、提取内容及更新记忆文件 |
| DL-12 | kl20-four-levels-of-delegation | 会话记忆保存、命令日志记录及系统启动初始化等无副作用操作应配置为 L4 Hooks 层级 |
| DL-13 | kl20-four-levels-of-delegation | API key 操作和权限变更因涉及安全和审批，必须收回控制权使用 L1 手动层级 |
| DL-14 | kl20-four-levels-of-delegation | 文章发布适合 L2 Cron 层级，可定时执行但需保留预览环节 |
| DL-15 | kl20-four-levels-of-delegation | 知识库处理适合 L3 Heartbeat 层级以进行批量检查和降噪，记忆保存适合 L4 Hooks 层级因无副作用 |
| DL-16 | kl20-four-levels-of-delegation | 从操作员心态转变为监管者心态的关键在于定好规则、抽查结果且仅在异常时介入 |

## 组内逻辑顺序

采用结构顺序：首先定义自动化的本质与四个层级的概念（DL-01~DL-05），接着阐述各层级的核心机制与适用场景（DL-06~DL-08），然后提供具体任务类型的层级匹配指南（DL-09~DL-15），最后总结心态转变的关键（DL-16）。
