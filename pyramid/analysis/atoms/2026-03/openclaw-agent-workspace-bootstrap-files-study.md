# OpenClaw Agent Workspace Bootstrap 文件体系学习

> 来源：[../../../../journal/2026-03-23/openclaw-agent-workspace-bootstrap-files-study.md](../../../../journal/2026-03-23/openclaw-agent-workspace-bootstrap-files-study.md)
> 缩写：OB

## Atoms

| 编号  | 类型 | 内容                                                                                               | 原文定位                    |
| ----- | ---- | -------------------------------------------------------------------------------------------------- | --------------------------- |
| OB-01 | 事实 | Bootstrap 文件在每次 agent 运行启动或上下文重组时由 resolveBootstrapContextForRun 从磁盘读入并注入   | 核心发现 > Bootstrap 文件加载机制 |
| OB-02 | 事实 | 同一轮多步对话中，Bootstrap 内容靠对话历史延续，并非每个 tool call 都重新全量加载                  | 核心发现 > Bootstrap 文件加载机制 |
| OB-03 | 事实 | 固定参与 bootstrap 的文件包括 AGENTS.md、SOUL.md、TOOLS.md、IDENTITY.md、USER.md                   | 核心发现 > 文件加载列表       |
| OB-04 | 事实 | HEARTBEAT.md、BOOTSTRAP.md、MEMORY.md 在子代理或 cron 会话中会被裁掉，仅常规会话加载                 | 核心发现 > 文件加载列表       |
| OB-05 | 事实 | memory/YYYY-MM-DD.md 日更日志不在自动 bootstrap 列表中，需靠记忆检索工具或显式读文件                 | 核心发现 > 文件加载列表       |
| OB-06 | 事实 | AGENTS.md 负责规则与流程，SOUL.md 负责人格与边界，USER.md 负责用户画像，IDENTITY.md 负责助手名片     | 核心发现 > 核心四件套分工     |
| OB-07 | 判断 | SOUL.md 侧重内在人格与原则（深度稳定），IDENTITY.md 侧重对外名片与元数据（浅层可展示）                 | 核心发现 > SOUL.md vs IDENTITY.md |
| OB-08 | 步骤 | HEARTBEAT.md 无事时回复 HEARTBEAT_OK，有事只发正文，文件为空或仅注释时可跳过本次心跳模型调用         | 核心发现 > HEARTBEAT.md 用法  |
| OB-09 | 事实 | Heartbeat 采用间隔型调度（会略漂移），Cron 采用表达式调度（可精确到秒）                              | 核心发现 > Heartbeat vs Cron  |
| OB-10 | 判断 | Heartbeat 适合多项检查合并与周期性巡检，Cron 适合定点任务、一次性提醒及独立模型运行                  | 核心发现 > Heartbeat vs Cron  |
| OB-11 | 经验 | bootstrap-extra-files 钩子只接受白名单文件名，无法注入 CONSTITUTION.md 等自定义文件名                | 核心发现 > 自定义 Markdown 扩展注入 |
| OB-12 | 经验 | 宪法类内容最务实的做法是写进 AGENTS.md 靠前位置，或在 AGENTS.md 中规定按需读取独立文件               | 核心发现 > 自定义 Markdown 扩展注入 |
| OB-13 | 事实 | 系统默认每个来源最多加载约 200 个技能，进 prompt 目录最多约 150 个，整段约 30,000 字符                 | 核心发现 > 300+ 技能场景的优化 |
| OB-14 | 经验 | 技能超预算时先降格为紧凑格式（省略描述），再截断条目；优化需在 AGENTS.md 写速查表并精简 SKILL.md 描述  | 核心发现 > 300+ 技能场景的优化 |
| OB-15 | 经验 | 不需要模型自动选中的技能应使用 disable-model-invocation 从目录里摘除以减少暴露面                     | 核心发现 > 300+ 技能场景的优化 |
| OB-16 | 事实 | TOOLS.md 仅用于记录本机命令与路径习惯的文字指导，不控制工具的实际可用性                              | 文件详解 > 其它文件           |
| OB-17 | 经验 | BOOTSTRAP.md 仅用于首次引导仪式，完成后应当删除以避免残留无用指令                                  | 文件详解 > 其它文件           |
| OB-18 | 判断 | 工作区文件被篡改等同于持久化提示注入，会改变 agent 的长期行为                                    | 安全性总结 > 通用安全原则     |
| OB-19 | 经验 | 禁止在工作区存放密钥（API Key、Token 等），应存放于密码管理器、环境变量或 ~/.openclaw/ 目录          | 安全性总结 > 通用安全原则     |
| OB-20 | 判断 | 工作区默认不是硬沙箱，仅是默认 cwd，需要隔离时必须配置 gateway sandbox                               | 安全性总结 > 通用安全原则     |
| OB-21 | 经验 | 工作区作为私有记忆，Git 备份必须使用 private repo 以防泄露                                         | 安全性总结 > 通用安全原则     |
| OB-22 | 事实 | Bootstrap 文件有单文件字符上限（默认 20,000）和总上限（默认 150,000），超出会自动截断                | 分析过程 > 关键约束           |
| OB-23 | 事实 | lightweight 上下文下，heartbeat 类运行通常只保留 HEARTBEAT.md 一份文件                             | 分析过程 > 关键约束           |
