# G77: OpenClaw 的 Bootstrap 上下文机制通过“分层文件注入 + 动态裁剪 + 预算熔断”策略，在保障 Agent 人格稳定性的同时实现资源可控

> Agent 启动时的上下文构建并非全量加载，而是基于会话类型动态裁剪核心文件，并受严格的字符预算与白名单机制约束，以防止提示注入与资源溢出。

## 包含的 Atoms

| 编号  | 来源                                     | 内容摘要 |
| ----- | ---------------------------------------- | -------- |
| OB-01 | openclaw-agent-workspace-bootstrap-files-study | Bootstrap 文件在每次 agent 运行启动或上下文重组时由 resolveBootstrapContextForRun 从磁盘读入并注入 |
| OB-02 | openclaw-agent-workspace-bootstrap-files-study | 同一轮多步对话中，Bootstrap 内容靠对话历史延续，并非每个 tool call 都重新全量加载 |
| OB-03 | openclaw-agent-workspace-bootstrap-files-study | 固定参与 bootstrap 的文件包括 AGENTS.md、SOUL.md、TOOLS.md、IDENTITY.md、USER.md |
| OB-04 | openclaw-agent-workspace-bootstrap-files-study | HEARTBEAT.md、BOOTSTRAP.md、MEMORY.md 在子代理或 cron 会话中会被裁掉，仅常规会话加载 |
| OB-05 | openclaw-agent-workspace-bootstrap-files-study | memory/YYYY-MM-DD.md 日更日志不在自动 bootstrap 列表中，需靠记忆检索工具或显式读文件 |
| OB-06 | openclaw-agent-workspace-bootstrap-files-study | AGENTS.md 负责规则与流程，SOUL.md 负责人格与边界，USER.md 负责用户画像，IDENTITY.md 负责助手名片 |
| OB-07 | openclaw-agent-workspace-bootstrap-files-study | SOUL.md 侧重内在人格与原则（深度稳定），IDENTITY.md 侧重对外名片与元数据（浅层可展示） |
| OB-08 | openclaw-agent-workspace-bootstrap-files-study | HEARTBEAT.md 无事时回复 HEARTBEAT_OK，有事只发正文，文件为空或仅注释时可跳过本次心跳模型调用 |
| OB-09 | openclaw-agent-workspace-bootstrap-files-study | Heartbeat 采用间隔型调度（会略漂移），Cron 采用表达式调度（可精确到秒） |
| OB-10 | openclaw-agent-workspace-bootstrap-files-study | Heartbeat 适合多项检查合并与周期性巡检，Cron 适合定点任务、一次性提醒及独立模型运行 |
| OB-11 | openclaw-agent-workspace-bootstrap-files-study | bootstrap-extra-files 钩子只接受白名单文件名，无法注入 CONSTITUTION.md 等自定义文件名 |
| OB-12 | openclaw-agent-workspace-bootstrap-files-study | 宪法类内容最务实的做法是写进 AGENTS.md 靠前位置，或在 AGENTS.md 中规定按需读取独立文件 |
| OB-13 | openclaw-agent-workspace-bootstrap-files-study | 系统默认每个来源最多加载约 200 个技能，进 prompt 目录最多约 150 个，整段约 30,000 字符 |
| OB-14 | openclaw-agent-workspace-bootstrap-files-study | 技能超预算时先降格为紧凑格式（省略描述），再截断条目；优化需在 AGENTS.md 写速查表并精简 SKILL.md 描述 |
| OB-15 | openclaw-agent-workspace-bootstrap-files-study | 不需要模型自动选中的技能应使用 disable-model-invocation 从目录里摘除以减少暴露面 |
| OB-16 | openclaw-agent-workspace-bootstrap-files-study | TOOLS.md 仅用于记录本机命令与路径习惯的文字指导，不控制工具的实际可用性 |
| OB-17 | openclaw-agent-workspace-bootstrap-files-study | BOOTSTRAP.md 仅用于首次引导仪式，完成后应当删除以避免残留无用指令 |
| OB-18 | openclaw-agent-workspace-bootstrap-files-study | 工作区文件被篡改等同于持久化提示注入，会改变 agent 的长期行为 |
| OB-19 | openclaw-agent-workspace-bootstrap-files-study | 禁止在工作区存放密钥（API Key、Token 等），应存放于密码管理器、环境变量或 ~/.openclaw/ 目录 |
| OB-20 | openclaw-agent-workspace-bootstrap-files-study | 工作区默认不是硬沙箱，仅是默认 cwd，需要隔离时必须配置 gateway sandbox |
| OB-21 | openclaw-agent-workspace-bootstrap-files-study | 工作区作为私有记忆，Git 备份必须使用 private repo 以防泄露 |
| OB-22 | openclaw-agent-workspace-bootstrap-files-study | Bootstrap 文件有单文件字符上限（默认 20,000）和总上限（默认 150,000），超出会自动截断 |
| OB-23 | openclaw-agent-workspace-bootstrap-files-study | lightweight 上下文下，heartbeat 类运行通常只保留 HEARTBEAT.md 一份文件 |

## 组内逻辑顺序

1. **加载机制与生命周期** (OB-01, OB-02, OB-17)：阐述 Bootstrap 的触发时机、缓存策略及临时文件的处理。
2. **文件体系与职责分工** (OB-03, OB-06, OB-07, OB-16)：定义核心文件集合及其在人格、规则、工具指导上的具体分工。
3. **动态裁剪策略** (OB-04, OB-05, OB-08, OB-23)：说明不同会话类型（常规/子代理/Cron/Heartbeat）下的文件加载差异。
4. **调度模式对比** (OB-09, OB-10)：区分 Heartbeat 与 Cron 在时间精度与应用场景上的不同。
5. **扩展限制与宪法注入** (OB-11, OB-12)：讨论额外文件注入的白名单限制及宪法类内容的替代方案。
6. **资源预算与熔断** (OB-13, OB-14, OB-15, OB-22)：详述技能加载数量限制、字符上限及超预算时的降级策略。
7. **安全边界与存储规范** (OB-18, OB-19, OB-20, OB-21)：强调工作区作为提示注入风险点的安全禁忌与隔离要求。
