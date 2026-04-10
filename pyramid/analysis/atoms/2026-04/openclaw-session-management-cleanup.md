# OpenClaw Session 管理与清理实战

> 来源：[../../../../journal/2026-04-04/openclaw-session-management-cleanup.md](../../../../journal/2026-04-04/openclaw-session-management-cleanup.md)
> 缩写：SM

## Atoms

| 编号  | 类型 | 内容                                                                                               | 原文定位                    |
| ----- | ---- | -------------------------------------------------------------------------------------------------- | --------------------------- |
| SM-01 | 事实 | OpenClaw 的 session 文件存储在 `~/.openclaw/agents/main/sessions/` 目录下                          | 背景与动机                  |
| SM-02 | 事实 | Session Key 是结构化标识而非随机名，格式包含 `agent:`、`cron:`、`hook:`、`gateway:` 等前缀         | 分析过程 > Session Key 的命名规则 |
| SM-03 | 事实 | `cron:` 开头的 session 由定时任务每次运行创建隔离会话，是 session 碎片化的主要原因                 | 分析过程 > Session 来源分布 |
| SM-04 | 事实 | `session.maintenance.mode` 默认值为 `warn`，仅记录日志报警而不执行自动清理                         | 分析过程 > 为什么会堆积     |
| SM-05 | 判断 | 将 maintenance mode 从 `warn` 改为 `enforce` 是解决 session 无限增长的必要决策                     | 方案设计 > 关键决策         |
| SM-06 | 判断 | 将 `pruneAfter` 从默认 30 天调整为 14 天更适合 cron session 无需长期保留的场景                     | 方案设计 > 关键决策         |
| SM-07 | 事实 | `maxDiskBytes` 为 sessions 目录设置磁盘硬上限，`highWaterBytes` 设定清理目标水位线（通常为上限的 75%） | 方案设计 > 关键决策         |
| SM-08 | 步骤 | 使用 `openclaw sessions cleanup --dry-run` 预览清理效果，确认将被 prune 或 cap 的条目数量          | 实现要点 > 执行步骤         |
| SM-09 | 步骤 | 执行 `openclaw sessions cleanup --enforce` 可立即将 session 条目数从数千削减至配置的上限值         | 实现要点 > 执行步骤         |
| SM-10 | 步骤 | 需在 `openclaw.json` 中配置 `session.maintenance` 块并再次执行 cleanup 以生效磁盘预算限制          | 实现要点 > 执行步骤         |
| SM-11 | 事实 | enforce 模式下的清理顺序为：Prune 过期会话 → Cap 超量条目 → Archive 转录文件 → Purge 过期归档 → Rotate 主文件 | 实现要点 > 维护机制工作原理 |
| SM-12 | 事实 | 维护机制在每次 session store 写入时自动触发，也可通过 CLI 手动强制执行                             | 实现要点 > 维护机制工作原理 |
| SM-13 | 经验 | 清理后磁盘占用略高于 highWaterBytes 是正常的，因为 `.deleted.*` 归档文件需等待 `resetArchiveRetention` 过期 | 验证与测试 > 清理效果对比   |
| SM-14 | 经验 | 若 cron session 增长仍过快，可单独配置 `cron.sessionRetention` 进行更细粒度的生命周期控制          | 后续演化                    |
| SM-15 | 判断 | 多用户场景下应将 DM 的 `dmScope` 从默认 `main` 切换为 `per-channel-peer` 以避免会话共享冲突        | 后续演化                    |
| SM-16 | 步骤 | 使用 `openclaw reset --scope sessions` 可执行核弹级重置，删除所有 session 数据                     | 实现要点 > Session 相关的常用 CLI 命令 |
