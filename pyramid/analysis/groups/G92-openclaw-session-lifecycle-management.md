# G92: OpenClaw 会话管理必须从“被动警告”转向“主动强制清理”，通过分层预算与细粒度生命周期控制解决 Cron 导致的碎片化爆炸

> 面对定时任务产生的海量会话碎片，仅靠日志警告无法遏制磁盘膨胀，必须实施基于时间窗口与磁盘水位的强制修剪策略。

## 包含的 Atoms

| 编号  | 来源                             | 内容摘要 |
| ----- | -------------------------------- | -------- |
| SM-01 | openclaw-session-management-cleanup | Session 文件存储于 `~/.openclaw/agents/main/sessions/` 目录 |
| SM-02 | openclaw-session-management-cleanup | Session Key 采用结构化标识（agent/cron/hook/gateway 前缀）而非随机名 |
| SM-03 | openclaw-session-management-cleanup | `cron:` 前缀会话由定时任务每次运行创建，是会话碎片化的主要根源 |
| SM-04 | openclaw-session-management-cleanup | 默认 `warn` 模式仅记录日志，不执行自动清理，无法解决无限增长问题 |
| SM-05 | openclaw-session-management-cleanup | 必须将 maintenance mode 切换为 `enforce` 以激活自动清理机制 |
| SM-06 | openclaw-session-management-cleanup | Cron 场景下应将 `pruneAfter` 从 30 天调整为 14 天以匹配短期保留需求 |
| SM-07 | openclaw-session-management-cleanup | 通过 `maxDiskBytes` 设硬上限，`highWaterBytes` 设清理目标水位（通常 75%） |
| SM-08 | openclaw-session-management-cleanup | 使用 `--dry-run` 预览清理效果，确认将被修剪或截断的条目数量 |
| SM-09 | openclaw-session-management-cleanup | 执行 `--enforce` 可立即将会话条目从数千削减至配置上限 |
| SM-10 | openclaw-session-management-cleanup | 需在 `openclaw.json` 配置 `session.maintenance` 并再次执行 cleanup 生效磁盘预算 |
| SM-11 | openclaw-session-management-cleanup | 清理顺序：Prune 过期 → Cap 超量 → Archive 转录 → Purge 归档 → Rotate 主文件 |
| SM-12 | openclaw-session-management-cleanup | 维护机制在每次写入时自动触发，也支持 CLI 手动强制执行 |
| SM-13 | openclaw-session-management-cleanup | 清理后磁盘略高于水位线属正常，因 `.deleted.*` 归档需等待保留期过期 |
| SM-14 | openclaw-session-management-cleanup | 针对 Cron 增长过快场景，可单独配置 `cron.sessionRetention` 进行细粒度控制 |
| SM-15 | openclaw-session-management-cleanup | 多用户场景需将 `dmScope` 切换为 `per-channel-peer` 以避免会话共享冲突 |
| SM-16 | openclaw-session-management-cleanup | `openclaw reset --scope sessions` 提供核弹级重置选项，删除所有会话数据 |

## 组内逻辑顺序

遵循“问题诊断（存储结构与碎片化根源）→ 策略决策（模式切换与参数调优）→ 执行验证（干跑与强制清理）→ 机制详解（清理顺序与触发时机）→ 特殊场景（细粒度控制与多用户隔离）→ 极端操作（全量重置）”的实战操作逻辑。
