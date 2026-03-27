# 用 ACP + Cursor Agent 替代 OpenClaw 原生编程——从文档分析到完整部署

> 来源：[../../../../journal/2026-03-27/acp-cursor-agent-integration.md](../../../../journal/2026-03-27/acp-cursor-agent-integration.md)
> 缩写：AC

## Atoms

| 编号  | 类型 | 内容                                                                                               | 原文定位                    |
| ----- | ---- | -------------------------------------------------------------------------------------------------- | --------------------------- |
| AC-01 | 判断 | OpenClaw 编程成本高的根本原因是读文件、写代码等操作大量消耗 Chat 模型上下文 token，而非日常对话      | 背景与动机                  |
| AC-02 | 事实 | ACP（Agent Client Protocol）允许 Gateway 将编程任务外包给外部 harness，由外部工具承担编码 token 成本 | 背景与动机                  |
| AC-03 | 事实 | ACP 体系分为"IDE 桥”（stdio 接 Gateway）和“运行时 + 外挂 harness"（后端驱动外部编码程序）两条线      | 分析过程：理解 ACP 体系     |
| AC-04 | 事实 | ACP 配置覆盖顺序为：bindings[].acp → agents.list[].runtime.acp → 全局 acp.*                         | 分析过程：理解 ACP 体系     |
| AC-05 | 经验 | 插件代码实现 `registerAcpRuntimeBackend` 后，必须在主配置文件中显式开启 `acp` 块才能生效            | 分析过程：理解 ACP 体系     |
| AC-06 | 判断 | 在 Gateway 模式下应禁用插件侧的 `maxSessions` 和 `idleTtlMinutes` 限制，避免与 Gateway 生命周期管理冲突 | 方案设计：整合 Cursor 为 ACP 后端 |
| AC-07 | 步骤 | 在 `openclaw.json` 中新增 `acp` 配置块，设置 `enabled: true` 及 `backend: "cursor"` 以激活运行时     | 实现要点：四处改动          |
| AC-08 | 步骤 | 为插件配置 `command`（agent.cmd 路径）、`model` 及 `permissionMode`，使其能被 ACP 运行时调用         | 实现要点：四处改动          |
| AC-09 | 经验 | 插件元数据文件 `openclaw.plugin.json` 需声明 `configSchema` 和 `uiHints`，并移除 Gateway 模式下无效的限制字段 | 实现要点：四处改动          |
| AC-10 | 步骤 | 在插件入口文件硬编码 `overrides = { maxSessions: 0, idleTtlMinutes: 0 }` 以在 Gateway 模式下禁用本地限制 | 实现要点：四处改动          |
| AC-11 | 步骤 | 新增 `openclaw cursor setup` 命令，用于自动配置所有 ACP 相关项并支持 `--dry-run` 预演               | 实现要点：四处改动          |
| AC-12 | 经验 | 修改配置解析逻辑使 `0` 值能穿透默认值处理，从而实现 `maxSessions <= 0` 跳过并发检查的效果           | 实现要点：四处改动          |
| AC-13 | 事实 | ACP 会话拥有独立 session key（格式 `agent:main:acp:<uuid>`），与 main agent 完全隔离且并行执行      | 运行时行为分析              |
| AC-14 | 事实 | ACP 长任务单轮上限受 Gateway `timeoutSeconds` 限制，JSON-RPC 层提供 24 小时安全兜底                 | 运行时行为分析              |
| AC-15 | 步骤 | 使用 `/acp status`、`/acp sessions` 或 CLI 命令 `openclaw cursor doctor` 监控 ACP 运行时状态         | 运行时行为分析              |
| AC-16 | 事实 | 可用模型列表需通过 `agent.cmd --list-models` 实时获取，包含 composer、claude、gpt 等 80+ 个模型     | 运行时行为分析              |
| AC-17 | 判断 | 将编码任务通过 ACP 外包给 Cursor CLI 是从成本角度使用 OpenClaw 的结构性必要措施，而非可选优化        | 核心洞察                    |
| AC-18 | 步骤 | 验证部署时需重启 Gateway 并执行 `/acp spawn cursor --cwd <仓库>` 观察回复                           | 后续演化                    |
| AC-19 | 判断 | 若需同时支持 Codex 或 Claude Code，可采用安装 acpx 作为第二后端的多后端并存方案                     | 后续演化                    |
