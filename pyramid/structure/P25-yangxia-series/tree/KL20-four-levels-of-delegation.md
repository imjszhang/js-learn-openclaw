# KL20 - 从 OpenClaw 官方文档里了解的时间哲学：工业时间与生物时间

> 关联项目：OpenClaw 自动化配置
> 素材来源：OpenClaw 官方文档深度研究（cron-jobs.md / heartbeat.md / cron-vs-heartbeat.md / hooks.md）

---

## SCQA

**S (情境)**  
为了搞懂怎么让 OpenClaw 自己跑起来，我花了一整个下午读官方文档。在读 cron-jobs.md 和 heartbeat.md 的时候，突然发现一个隐藏的主题——时间不是只有一种形态。

**C (冲突)**  
Cron 代表工业时间：精确、机械、可预测，像工厂流水线。Heartbeat 代表生物时间：有机、灵活、会自己判断，像心脏跳动。我们习惯了前者，却忽略了后者才是和 AI 共生的正确姿势。

**Q (问题)**  
怎么从官方文档里读出这种时间哲学？怎么把"钟表时间"切换到"心跳时间"？

**A (答案)**  
官方文档里藏着三种时间形态：工业时间（Cron）处理机械任务，生物时间（Heartbeat）处理判断任务，事件时间（Hooks）处理反射任务。三种时间各司其职，AI 才能真正"活"起来。

---

## Key Line 树

### KL01 - 从文档对比中发现时间哲学

**支撑**：
1. 读文档的出发点：让 OpenClaw 自己跑起来
2. 三份关键文档：cron-jobs.md、heartbeat.md、cron-vs-heartbeat.md、hooks.md
3. 原文对比的意外发现：
   - "Cron runs scheduled jobs at **fixed times**"
   - "Heartbeat runs periodic agent turns... **without spamming you**"
4. 核心洞察：这不是技术差异，是时间观的根本分歧

**引入钩子**：
> "如果你把 OpenClaw 的自动化文档放在一起读，会发现一个隐藏的主题——时间不是只有一种形态。"

---

### KL02 - 工业时间：Cron 的精确与机械（文档原文）

**支撑**：
1. **文档定义**：cron-jobs.md 中的核心描述
   > "Cron is the Gateway's built-in scheduler. It persists jobs, wakes the agent at the right time..."
   
2. **哲学隐喻**：工业时代的钟表——时间是可以切分的、可预测的
3. **官方配置示例**：
   ```yaml
   schedule:
     kind: cron
     expr: "0 9 * * *"  # 每天9点
   ```
4. **特点**：精确时间点、机械执行、确定性输出
5. **文档揭示的局限**：不会判断"今天有没有必要做"

**关键洞察**：Cron 适合"做什么"确定、"什么时候做"也确定的任务。

---

### KL03 - 生物时间：Heartbeat 的有机与降噪（文档原文）

**支撑**：
1. **文档定义**：heartbeat.md 中的核心描述
   > "Heartbeat runs **periodic agent turns** in the main session so the model can surface anything that needs attention **without spamming you**."
   
2. **与 Cron 的官方对比**（cron-vs-heartbeat.md）：
   | Cron | Heartbeat |
   |------|-----------|
   | Fixed times | Periodic turns |
   | Exact schedule | Flexible cadence |
   | Always runs | Conditional output |

3. **关键机制**：`HEARTBEAT_OK` = "没事，别打扰你"
4. **哲学隐喻**：生物节律——心脏不是精确到秒跳动，而是按需调节
5. **核心差异**：Heartbeat 把"判断权"部分移交给了 AI

**文档原文的微妙之处**：
> "If nothing needs attention, reply exactly: HEARTBEAT_OK"

这不是技术指令，是**交互契约**——沉默即无事。

---

### KL04 - 事件时间：Hooks 的条件反射（文档原文）

**支撑**：
1. **文档定义**：hooks.md 中的描述
   > "Hooks provide an extensible event-driven system for automating actions in response to agent commands and events."
   
2. **触发方式**：事件发生即响应，无需唤醒周期
3. **官方示例**：
   - `session-memory`: `/new` 时自动保存
   - `command-logger`: 自动记录审计
   - `boot-md`: 启动时自动初始化

4. **三种时间形态官方对比表**（从文档提取）：

| 时间形态 | 触发方式 | 决策位置 | 文档定位 |
|---------|---------|---------|---------|
| **工业时间** (Cron) | 精确时间点 | 人 | Scheduled jobs |
| **生物时间** (Heartbeat) | 周期唤醒 | AI 判断 | Periodic turns |
| **事件时间** (Hooks) | 事件触发 | 系统 | Event-driven |

---

### KL05 - 官方文档里的实践映射

**支撑**：
1. **工业时间适用场景**（文档暗示）：
   - 数据处理："prism processing"
   - 监控采集："X.com monitoring"
   - 内容同步："blog sync"

2. **生物时间适用场景**（文档暗示）：
   - 记忆维护："update memory files"
   - 状态检查："surface anything that needs attention"

3. **事件时间适用场景**（文档暗示）：
   - 会话保存："/new command"
   - 日志记录："automatic audit"

**文档给出的选择指南**（cron-vs-heartbeat.md 原文）：
> "Exact timing matters → Cron"  
> "Conversational context matters → Heartbeat"  
> "Event-driven reaction → Hooks"

---

### KL06 - 从文档到实践：我的三时间配置

**支撑**：
1. **工业时间 (Cron)** —— 来自文档的机械任务清单：
   ```yaml
   prism-auto-process: 每 60 分钟
   link-collector: 每 30 分钟
   X.com 监控: 每 30 分钟
   ClawHub 博客同步: 每 2 小时
   ```

2. **生物时间 (Heartbeat)** —— 来自文档的判断任务：
   ```markdown
   # HEARTBEAT.md
   - 回顾当天对话，提取重要内容
   - 更新 memory/YYYY-MM-DD.md
   - 如有重大事项，同步更新 MEMORY.md
   ```

3. **事件时间 (Hooks)** —— 来自文档的反射任务：
   - `session-memory`: 文档示例钩子
   - `command-logger`: 文档审计示例
   - `boot-md`: 文档初始化示例

**配置全景图**：

```
┌─────────────────────────────────────────────────┐
│  工业时间 (Cron) - 精确、机械、可预测              │
│  （来自 cron-jobs.md 官方定义）                   │
├─────────────────────────────────────────────────┤
│  • prism-auto-process: 每 60 分钟               │
│  • link-collector: 每 30 分钟                   │
│  • X.com 监控: 每 30 分钟                        │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  生物时间 (Heartbeat) - 周期、判断、降噪         │
│  （来自 heartbeat.md 官方定义）                  │
├─────────────────────────────────────────────────┤
│  • 记忆维护: 每 30 分钟检查                      │
│  • 状态汇总: 当日对话回顾                        │
│  • HEARTBEAT_OK = "without spamming you"        │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  事件时间 (Hooks) - 即时、反射、无感知           │
│  （来自 hooks.md 官方定义）                      │
├─────────────────────────────────────────────────┤
│  • session-memory: /new 时自动保存               │
│  • command-logger: 自动审计日志                  │
│  • boot-md: 启动时自动初始化                     │
└─────────────────────────────────────────────────┘
```

---

### KL07 - 最难的切换：从控制到共生（文档的启示）

**支撑**：
1. **文档揭示的工业思维惯性**：
   - "Exact timing matters" —— 我们对确定性的执念
   - "wakes the agent at the right time" —— 精确控制的幻觉

2. **文档暗示的生物思维要求**：
   - "without spamming you" —— 尊重注意力完整性
   - "model can surface anything" —— 把判断权交给 AI

3. **信任的建立过程**：从"到点必响"到"按需汇报"
4. **降噪的心理收益**：注意力不再被切碎

**文档里的智慧**（cron-vs-heartbeat.md）：
> "Optimize token usage" —— 不只是省钱，是减少认知负载。

---

### KL08 - 官方文档给出的迁移建议

**支撑**：
1. **文档里的决策树**（cron-vs-heartbeat.md 原文）：
   - Exact timing → Cron
   - Batching similar checks → Heartbeat
   - Event-driven → Hooks

2. **三阶迁移路径**（从文档推断）：
   - **第一阶**：识别任务类型（文档的选择指南）
   - **第二阶**：先叠加，再替换（文档支持混合使用）
   - **第三阶**：接受"不完美"（Heartbeat 的 flexible cadence）

3. **具体行动清单**（基于文档）：
   - [ ] 阅读 cron-vs-heartbeat.md 官方对比
   - [ ] 列出你的"检查型任务"
   - [ ] 写一个 HEARTBEAT.md 试验
   - [ ] 对比两种模式的体验

---

## 素材清单

- [x] OpenClaw 官方文档全文（cron-jobs.md、heartbeat.md、cron-vs-heartbeat.md、hooks.md）
- [x] 文档关键原文对比（fixed times vs without spamming you）
- [x] 官方决策表（Exact timing / Conversational context / Event-driven）
- [x] 三时间形态对比表（从文档提取）
- [x] 我的实践配置（基于文档建议的分层清单）

---

## 开头类型

**推荐**：文档研究视角引入

> "为了搞懂怎么让 OpenClaw 自己跑起来，我花了一整个下午读官方文档。在读 cron-jobs.md 和 heartbeat.md 的时候，突然发现一个隐藏的主题——时间不是只有一种形态。"

**备选**：概念对比引入
> "OpenClaw 的官方文档里藏着一套时间哲学。不是每个人都读得出来。"

---

## 系列衔接

- **承接**：KL09（Harness Engineering，设计规则）、KL13（记忆系统，连续性）
- **递进**：从"怎么设计系统"到"怎么从文档里读出哲学"——阅读方法的升维
- **呼应**：系列规约 §4 的自动化成果——这是理解官方设计哲学的结果

---

_重构时间：2026-04-04_  
_状态：框架完成，待正文创作_
