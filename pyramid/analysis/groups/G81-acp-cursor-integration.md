# G81: ACP 架构通过将编码任务外包给外部 Harness，从结构上解决了 OpenClaw 原生编程的高 Token 成本难题

> 将读文件、写代码等高消耗操作剥离至外部 ACP 运行时（如 Cursor），是降低 Chat 模型上下文成本并实现长任务并行执行的必要架构演进。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| AC-01 | acp-cursor-agent-integration | OpenClaw 编程成本高的根本原因是读文件、写代码等操作大量消耗 Chat 模型上下文 token，而非日常对话 |
| AC-02 | acp-cursor-agent-integration | ACP（Agent Client Protocol）允许 Gateway 将编程任务外包给外部 harness，由外部工具承担编码 token 成本 |
| AC-03 | acp-cursor-agent-integration | ACP 体系分为"IDE 桥”（stdio 接 Gateway）和“运行时 + 外挂 harness"（后端驱动外部编码程序）两条线 |
| AC-04 | acp-cursor-agent-integration | ACP 配置覆盖顺序为：bindings[].acp → agents.list[].runtime.acp → 全局 acp.* |
| AC-05 | acp-cursor-agent-integration | 插件代码实现 `registerAcpRuntimeBackend` 后，必须在主配置文件中显式开启 `acp` 块才能生效 |
| AC-06 | acp-cursor-agent-integration | 在 Gateway 模式下应禁用插件侧的 `maxSessions` 和 `idleTtlMinutes` 限制，避免与 Gateway 生命周期管理冲突 |
| AC-07 | acp-cursor-agent-integration | 在 `openclaw.json` 中新增 `acp` 配置块，设置 `enabled: true` 及 `backend: "cursor"` 以激活运行时 |
| AC-08 | acp-cursor-agent-integration | 为插件配置 `command`（agent.cmd 路径）、`model` 及 `permissionMode`，使其能被 ACP 运行时调用 |
| AC-09 | acp-cursor-agent-integration | 插件元数据文件 `openclaw.plugin.json` 需声明 `configSchema` 和 `uiHints`，并移除 Gateway 模式下无效的限制字段 |
| AC-10 | acp-cursor-agent-integration | 在插件入口文件硬编码 `overrides = { maxSessions: 0, idleTtlMinutes: 0 }` 以在 Gateway 模式下禁用本地限制 |
| AC-11 | acp-cursor-agent-integration | 新增 `openclaw cursor setup` 命令，用于自动配置所有 ACP 相关项并支持 `--dry-run` 预演 |
| AC-12 | acp-cursor-agent-integration | 修改配置解析逻辑使 `0` 值能穿透默认值处理，从而实现 `maxSessions <= 0` 跳过并发检查的效果 |
| AC-13 | acp-cursor-agent-integration | ACP 会话拥有独立 session key（格式 `agent:main:acp:<uuid>`），与 main agent 完全隔离且并行执行 |
| AC-14 | acp-cursor-agent-integration | ACP 长任务单轮上限受 Gateway `timeoutSeconds` 限制，JSON-RPC 层提供 24 小时安全兜底 |
| AC-15 | acp-cursor-agent-integration | 使用 `/acp status`、`/acp sessions` 或 CLI 命令 `openclaw cursor doctor` 监控 ACP 运行时状态 |
| AC-16 | acp-cursor-agent-integration | 可用模型列表需通过 `agent.cmd --list-models` 实时获取，包含 composer、claude、gpt 等 80+ 个模型 |
| AC-17 | acp-cursor-agent-integration | 将编码任务通过 ACP 外包给 Cursor CLI 是从成本角度使用 OpenClaw 的结构性必要措施，而非可选优化 |
| AC-18 | acp-cursor-agent-integration | 验证部署时需重启 Gateway 并执行 `/acp spawn cursor --cwd <仓库>` 观察回复 |
| AC-19 | acp-cursor-agent-integration | 若需同时支持 Codex 或 Claude Code，可采用安装 acpx 作为第二后端的多后端并存方案 |

## 组内逻辑顺序

遵循“问题背景与架构原理 (AC-01~03) → 配置优先级与生效机制 (AC-04~05) → 具体实施步骤与代码修改 (AC-06~12) → 运行时特性与隔离机制 (AC-13~16) → 核心价值结论与验证运维 (AC-17~19)"的逻辑顺序。
