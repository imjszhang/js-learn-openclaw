# OpenClaw Session 管理与清理实战

> 日期：2026-04-04
> 项目：openclaw
> 类型：问题排查 / 调研分析
> 来源：Cursor Agent 对话

---

## 目录

1. [背景与动机](#1-背景与动机)
2. [分析过程](#2-分析过程)
3. [方案设计](#3-方案设计)
4. [实现要点](#4-实现要点)
5. [验证与测试](#5-验证与测试)
6. [后续演化](#6-后续演化)

---

## 1. 背景与动机

OpenClaw 的 `~/.openclaw/agents/main/sessions/` 目录下积累了大量随机命名的 session 文件，需要弄清楚这些 session 是什么、怎么来的、怎么管理。

**清理前的现状：**

| 指标 | 数值 |
| ---- | ---- |
| Session 条目数 | 2666 |
| `sessions.json` 大小 | 55 MB |
| JSONL 转录文件数 | 4429 |
| 总文件数 | 4739 |
| 磁盘占用 | 247 MB |
| maintenance mode | `warn`（只报警不清理） |

## 2. 分析过程

### Session Key 的命名规则

Session key 并非"随机名"，而是由路由规则自动生成的结构化标识：

| Key 模式 | 来源 | 示例 |
| ---- | ---- | ---- |
| `agent:<agentId>:main` | DM 主会话 | `agent:main:main` |
| `agent:<agentId>:<channel>:direct:<peerId>` | 按渠道+发送者隔离的 DM | `agent:main:feishu:direct:ou_xxx` |
| `agent:<agentId>:<channel>:group:<id>` | 群组会话 | `agent:main:feishu:group:xxx` |
| `cron:<jobId>` | Cron 定时任务（每次运行创建隔离 session） | `cron:daily-report` |
| `hook:<uuid>` | Webhook 触发 | `hook:abc123` |
| `gateway:...` | Gateway 调用（`openclaw agent` 等） | -- |

### Session 来源分布

从列表观察，2666 条 session 中：
- **绝大多数是 `cron:` 开头** -- 每次 cron 运行都创建隔离 session，是碎片化主因
- **大量 `gateway:` 开头** -- 来自 gateway 调用
- **少量 `math-`、`hook:`、`feish...` 群组** -- 专用/webhook/飞书群会话
- **1 个 `main`** -- 主会话
- 最老的 session 已 59-62 天

### 为什么会堆积

根本原因：`session.maintenance.mode` 默认是 `warn`，只在日志中报警但不执行清理。随着 cron job 高频运行，session 条目持续增长且从未被自动回收。

### 核心文档参考

| 文档 | 内容 |
| ---- | ---- |
| `docs/concepts/session.md` | Session 路由、生命周期、maintenance 配置、session key 映射规则 |
| `docs/cli/sessions.md` | `openclaw sessions` 和 `sessions cleanup` CLI 参考 |
| `docs/reference/session-management-compaction.md` | 两层持久化（sessions.json + JSONL）、磁盘控制、cron session 保留 |
| `docs/gateway/configuration-reference.md` | `session.*` 全字段参考 |

## 3. 方案设计

### 管理思路（由浅到深）

1. **查看现状**：`openclaw sessions --all-agents` 列出所有 session
2. **清理存量**：`openclaw sessions cleanup --dry-run` 预览 → `--enforce` 执行
3. **自动维护**：配置 `session.maintenance.mode: "enforce"` 长期自动管理
4. **源头治理**：通过 `dmScope`、`reset` 策略减少 session 碎片化

### 关键决策

| 决策 | 选择 | 理由 |
| ---- | ---- | ---- |
| maintenance mode | `enforce` | `warn` 只报警不清理，导致无限增长 |
| pruneAfter | `14d` | 默认 30d 过于保守，cron session 无需长期保留 |
| maxEntries | `500` | 使用默认值，对当前使用量足够 |
| maxDiskBytes | `200mb` | 为 sessions 目录设硬上限，防止磁盘失控 |
| highWaterBytes | `150mb` | 清理目标水位线，设为 maxDiskBytes 的 75% |
| resetArchiveRetention | `7d` | `.deleted.*` 和 `.reset.*` 归档文件只保留 7 天 |

## 4. 实现要点

### 执行步骤

**Step 1：检查现状**

```bash
openclaw sessions --all-agents          # 列出所有 session（2666 条）
openclaw sessions cleanup --dry-run     # 预览清理效果
```

dry-run 结果：2666 条中 540 条会因超 30 天被 prune，1626 条会因超 500 上限被 cap。

**Step 2：执行清理**

```bash
openclaw sessions cleanup --enforce     # 第一次：2666 → 500
```

`sessions.json` 从 55MB 降至 2.2MB，但 JSONL 孤立文件仍在。

**Step 3：添加磁盘预算配置并再次清理**

在 `~/.openclaw/openclaw.json` 中添加 `session` 配置块：

```json5
{
  "session": {
    "maintenance": {
      "mode": "enforce",
      "pruneAfter": "14d",
      "maxEntries": 500,
      "rotateBytes": "10mb",
      "maxDiskBytes": "200mb",
      "highWaterBytes": "150mb",
      "resetArchiveRetention": "7d"
    }
  }
}
```

```bash
openclaw sessions cleanup --enforce     # 第二次：磁盘预算生效，500 → 68
```

### 维护机制工作原理

`mode: "enforce"` 时，清理按以下顺序执行：

1. Prune 超过 `pruneAfter` 未活跃的 session
2. Cap 超过 `maxEntries` 的最旧条目
3. Archive 已删除条目的 transcript 文件
4. Purge 过期的 `.deleted.*` / `.reset.*` 归档
5. Rotate 超过 `rotateBytes` 的 `sessions.json`
6. 如果设了 `maxDiskBytes`，按最旧优先清理到 `highWaterBytes`

维护在每次 session store 写入时自动执行，也可通过 `openclaw sessions cleanup` 手动触发。

### Session 相关的常用 CLI 命令

| 命令 | 用途 |
| ---- | ---- |
| `openclaw sessions` | 列出默认 agent 的 session |
| `openclaw sessions --all-agents` | 列出所有 agent 的 session |
| `openclaw sessions --active 120` | 只看最近 120 分钟内活跃的 |
| `openclaw sessions --json` | JSON 格式输出 |
| `openclaw sessions cleanup --dry-run` | 预览清理效果 |
| `openclaw sessions cleanup --enforce` | 强制执行清理 |
| `openclaw sessions cleanup --enforce --active-key "agent:main:main"` | 保护指定 session |
| `openclaw reset --scope sessions` | 核弹级重置（删除所有 session） |

## 5. 验证与测试

### 清理效果对比

| 指标 | 清理前 | 清理后 | 变化 |
| ---- | ------ | ------ | ---- |
| Session 条目 | 2666 | 72 | -97.3% |
| `sessions.json` | 55 MB | 2.4 MB | -95.6% |
| JSONL 文件数 | 4429 | 815 | -81.6% |
| 总文件数 | 4739 | 1093 | -76.9% |
| 磁盘占用 | 247 MB | 152 MB | -38.5% |

清理后 72 条 session 全部是最近 4 小时内的活跃会话，分布：
- `cron:` -- 定时任务隔离会话（占多数）
- `gateway:` -- 飞书等渠道消息通过 gateway 调用
- `math-` -- 数学相关专用会话

磁盘 152MB 略高于 highWaterBytes (150MB)，因 `.deleted.*` 归档文件未到 7 天过期时间，后续会自动清理。

## 6. 后续演化

- **观察自动维护效果**：配置生效后，每次 session store 写入会自动执行维护，观察一周看 session 数量和磁盘是否稳定在合理范围
- **调整 cron session 保留策略**：如果 cron session 仍然增长过快，可考虑配置 `cron.sessionRetention` 来单独控制 cron session 生命周期
- **dmScope 调整**：当前 DM 使用默认 `main`（所有 DM 共享一个 session），如果后续多用户场景增加，应切换到 `per-channel-peer`
- **磁盘预算微调**：根据实际使用量，可能需要调整 `maxDiskBytes` / `highWaterBytes` 的比例

---
