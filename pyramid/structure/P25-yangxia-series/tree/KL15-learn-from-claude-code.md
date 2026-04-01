# KL15：从 Claude Code 源码学习 OpenClaw Harness Engineering 配置

> 所属视角：P25-yangxia-series
> 上层论点：通过 37 天真实"养虾"记录，展示从养出第一只社交龙虾 JS_BestAgent 到构建出包含五个项目的 AI 工具小生态的完整路径——不是教程，是一个普通人从"装完发呆"到"离不开它"的成长日记。

## Key Line

> 51万行代码裸奔后，我终于看懂了自己的 OpenClaw 缺什么——Claude Code 的六大技术/八个 Skill，正是 Harness Engineering 的最佳实践教材。

---

## 顶层论点

**KL15-01**：Claude Code 泄露的本质不是灾难，而是一次「工程透明化」实验——它证明了**工程外壳 > 模型本身**

**KL15-02**：六大技术杀手锏（实时上下文/激进缓存/专用工具链/上下文压缩/结构化记忆/子 Agent 并行）在 OpenClaw 中都有对应配置策略

**KL15-03**：社区提炼的 8 个 Agent Skill（协调者/并发/对抗验证/自我合理化防护/Worker 指令/记忆类型/记忆防护/轻量探索）可直接转化为 Harness 配置清单

**KL15-04**：最深刻的启示是「自我合理化防护」——当 AI 说"代码看起来正确"时，正确行动是运行它

---

## 引用 Synthesis

- **S27** (OpenClaw 个人实践知识库)：技能系统、Harness Engineering
- **S45** (OpenClaw 个人实践知识库)：三层记忆架构
- **S49** (OpenClaw 个人实践知识库)：ACP 外包策略
- **S62** (OpenClaw 个人实践知识库)：Cursor Agent 实践

## 引用 Groups

- **G07** (AGENTS.md/SOUL.md/USER.md 配置)：对应实时上下文加载
- **G34** (JS-Eyes 浏览器自动化)：对应专用工具链
- **G41** (Memory-Core 配置)：对应激进缓存复用
- **G50** (消息路由与通道配置)：对应结构化会话
- **G52** (知识库桥接与检索)：对应上下文压缩
- **G57** (知识棱镜分层处理)：对应记忆类型系统
- **G64** (Session 管理与子代理)：对应子 Agent 并行
- **G77** (Harness Engineering 四层架构)：理论基础
- **G78** (委托→协作的正确姿势)：对应协调者模式
- **G81** (ACP 外包策略实践)：对应成本优化

---

## 展开状态

- [x] KL15-01：工程透明化与工程外壳 > 模型
- [x] KL15-02：六大技术 → OpenClaw 配置映射
- [x] KL15-03：八个 Skill → Harness 实战清单
- [x] KL15-04：自我合理化防护的深层启示

## 文章输出

- **文件**: `outputs/yangxia-series/P25-yangxia-series/15.md`
- **标题**: 当 Claude Code 被扒光，我看到了 OpenClaw 的未来
- **副标题**: 51万行源码泄露，反而证明了一件事
- **发布日期**: 2026-04-01

---

## 原始素材（完整引用）

### 文章来源
- **标题**: Claude Code源码「换壳」反杀，全网疯狂克隆！Anthropic封杀失败
- **来源**: 新智元
- **URL**: https://mp.weixin.qq.com/s/ASZRSMcXfomyVcr9YT70Rg
- **记录 ID**: efsim27ns3mr4tc
- **泄露时间**: 2026-03-31
- **泄露规模**: 51.2 万行 TypeScript，1900 个源文件

### 核心人物
- **Sigrid Jin** (instructkr): 源码重写者，一年消耗 250 亿 Claude Token，WSJ 报道对象
- **Sebastian Raschka**: AI 大牛，六大技术拆解
- **Chaofan Shou**: 安全大佬，首次爆料者
- **Karpathy**: 点评 "将 Claude Code 龙虾化"

### 六大技术杀手锏（Sebastian Raschka 拆解）

**1. 实时仓库上下文加载**
- 启动时自动读取主分支、当前分支、最近提交记录
- 加上 CLAUDE.md，构建动态项目全景
- 网页版上传文件根本做不到

**2. 激进的 Prompt 缓存复用**
- 系统提示词被边界标记拆成静态和动态两部分
- 静态部分全局缓存，不用每次重建重处理
- 省下大量计算开销

**3. 专用工具链**
- Grep 工具（权限控制优于 Bash 裸 grep）
- Glob 工具做文件发现
- LSP（语言服务器协议）工具做调用层级分析和引用查找
- 网页版把代码当静态文本，Claude Code 把代码当活的项目

**4. 极致压缩上下文膨胀**
- 文件读取去重（文件没变就不重新处理）
- 工具结果过大时写磁盘只留预览+引用
- 长上下文自动截断和摘要压缩

**5. 结构化会话记忆**
- 每次对话维护结构化 Markdown 文件
- 包含：会话标题、当前状态、任务规格、文件与函数、工作流、错误与修正、代码库文档、学习笔记、关键结果、工作日志
- Raschka: "这就是我们人类写代码的方式，随手记笔记和摘要"

**6. Fork 和子 Agent 并行**
- 分叉出的 Agent 复用父级缓存，同时感知可变状态
- 可以在不污染主 Agent 循环的情况下做摘要、记忆提取或后台分析

**核心结论**: Claude Code 的强大不靠模型，靠这套软件「外壳」。实时上下文加载、激进缓存复用、专用工具链、上下文压缩、结构化记忆、子 Agent 并行，这些工程优化的总和才是真正的护城河。

### 八个 Agent Skill（huo0 提炼）

**项目主页**: https://github.com/ChinaSiro/claude-code-sourcemap

**1. Coordinator Orchestrator（协调者模式）**
- 核心五个字：你是指挥官
- 调度 Worker 做研究、实现、验证，自己只做综合决策
- 最狠规则：禁止「懒委托」
- 不能写「基于你的发现，修复这个 bug」
- 必须消化研究结果后给出精确到文件路径和行号的指令

**2. Task Concurrency Patterns（任务并发模式）**
- 只读任务自由并行
- 写操作同一文件区域串行
- 验证可以与不同区域的实现并行
- 用 AsyncLocalStorage 做上下文隔离，确保 Worker 不会互相踩踏

**3. Adversarial Verification（对抗性验证）**
- 第一句话：「你的目标不是确认实现正确，而是尝试打破它」
- 两个已知失败模式：
  - 读了代码就写 PASS 从不跑命令
  - 看到测试通过就放行没注意一半功能是空的
- 不接受「代码看起来正确」这种结论

**4. Self-Rationalization Guard（自我合理化防护）**
- Agent 版「认知行为治疗」
- AI 说「代码看起来正确」→ 正确行动是运行它
- AI 说「这个要花太久了」→ 正确行动是告知预计时间然后做
- AI 说「先处理简单的部分」→ 正确行动是先做最难的
- 如果你在写解释而不是运行命令，停，运行命令

**5. Worker Prompt Craft（Worker 指令编写）**
- Worker 看不到你的对话上下文
- 每条指令必须自包含，包含文件路径、行号、完成标准
- 绝对禁止「修复我们讨论的 bug」这种写法

**6. Memory Type System（记忆类型系统）**
- 记忆分四类：
  - user（画像）
  - feedback（纠正）
  - project（状态）
  - reference（指针）
- 「绝对不记」清单：代码模式、Git 历史、调试方案（grep 和 git log 能查到的不该占记忆空间）

**7. Smart Memory Guard（记忆防护）**
- 三道防线：
  - 漂移防护（行动前验证文件是否还存在）
  - 膨胀检查（超 5KB 自动瘦身）
  - 写入过滤（6 个月后还有用吗）

**8. Lightweight Explorer（轻量探索）**
- 探索任务三个属性：只读、快速、低成本
- 不知道位置广搜，知道位置精确读，搜不到换策略
- 独立搜索必须并行

### 隐藏功能彩蛋

**Buddy System（电子宠物系统）**
- 内置完整的电子宠物系统
- Yadong Xie 做了交互界面：https://claude-buddy.vercel.app/#dragon

**KAIROS（终极自主架构）**
- 7x24 小时始终在线、自主的 Claude
- 不用提出需求，自己就会跑去干活
- Karpathy 点评：这些功能明显是将 Claude Code「龙虾化」

**Capybara 模型**
- 代码中发现最高层级 Claude Mythos 模型，代号 Capybara

### 泄露原因分析

- **疑似原因**: Bun 工具链漏洞（GitHub issue #28001）
- **具体失误**: npm 注册表中的一个 map 文件配置疏忽
- **OpenClaw 之父关注**: 下场关注此事件
- **社区调侃**: "这是愚人节送上的最佳礼物"

### 关键引用

**Sigrid Jin**: 
> "最终的成果是一个符合『净室设计』标准的 Python 重写版。它完美复刻了 Claude Code 的 AI 智能体框架的架构模式，绝对没有抄袭任何专有源码。"

**新智元总结**:
> "上下文压缩怎么做？Agent 长期记忆怎么管理？多 Agent 怎么防偷懒？MCP 协议怎么安全调度？这些原本带着机密色彩的工程问题，现在全有了公开答案。"

### 参考链接

- 重写版仓库: https://github.com/instructkr/claw-code
- 8 个 Skill 项目: https://github.com/ChinaSiro/claude-code-sourcemap
- Bun 漏洞: https://github.com/oven-sh/bun/issues/28001
- Raschka 拆解: https://x.com/rasbt/status/2038980345316413862

---

## 补充素材 2：KAIROS 深度解析（量子位报道）

### 文章来源
- **标题**: Anthropic被逼急了！亲生龙虾意外曝光，Karpathy：这就是Claude Claw
- **来源**: 量子位 (QbitAI)
- **URL**: https://mp.weixin.qq.com/s/sPFY7yKV3Xs80Bkpgbhrtg
- **记录 ID**: k9pswixnvjrj2nn
- **发现者**: Ole Lehmann

### Karpathy 核心观点

**Claw 是 AI 的下一个进化方向**（2月预言）

Karpathy 指出，Claw 类产品是继 Chat 和 Code 之后，AI 技术栈里的一个全新层级：
- **Chat**: 用户自己开车（主动发指令）
- **Code**: 用户坐在副驾当导航（协作编码）
- **Claw**: 用户可以彻底摆烂，躺在后排睡大觉（完全自主）

自主权越来越高，主动性越来越强。

### KAIROS 核心能力详解

**1. 主动「龙虾爪」**
- 会主动找你的 Claude
- 你还没开口，它可能突然出现，拍拍你肩膀，告诉你它刚刚干了啥
- 24 小时后台运行，你工作也好，睡觉也罢，它一直都在

**心跳机制**（与 OpenClaw 完全一致）：
- 每隔几秒，KAIROS 就会收到一个「心跳」Prompt
- 内容大概是：「醒醒，看看现在有啥值得干的活儿没？」
- 然后判断：是动手，还是继续安静待着

**行动范围**（无需用户开口）：
- 修代码 bug
- 回消息
- 更新文件
- 执行任务

**2. 三大专属技能**（原生整合，开箱即用）

| 技能 | 功能 | OpenClaw 对应 |
|------|------|---------------|
| 推送通知 | 主动给手机或电脑发消息，不用开终端 | `nodes notify` |
| 文件投递 | 直接把生成的内容发给你，不用开口要 | `sessions_send` |
| PR 订阅 | 盯着 GitHub，代码变动自动响应 | `github` skill + `cron` |

**3. 个性化与记忆**

**日报机制**：
- 每天写日报，记录：看到了什么、怎么判断的、做了什么
- 用户可完整回溯行为
- 记录追加式，不能删
- 养得越久越好用，跨会话持续

**4. autoDream（梦境机制）**

为解决上下文指数级膨胀问题：
- 晚上运行 autoDream 流程
- 把白天学到的东西整合一遍
- 重新整理记忆
- **设计诗意**：让人类设计太神奇，谁想过睡觉居然是一种处理上下文膨胀的巧妙设计

### 核心痛点与商业化障碍

**Token 消耗问题**：
- 长上下文导致 Token 指数级膨胀
- 啥都没干，早上「hi」可能先烧掉十几万 token
- Anthropic 的用量设计「太反人类」，Pro 用户被当免费用户整
- 跑个任务执行到一半报错：一周额度已用完

**OpenClaw 的优势**：
- 本地部署，上下文成本可控
- 可用 Coding Plan 优化
- 不用像 KAIROS 那样烧巨额 Token

### 行业趋势：后提示词时代

**定义**：
- Prompt 不再是唯一的触发方式
- AI 在后台默默工作的时间会越来越长
- AI 不再是拿到 Prompt 才干活，而是先干，完事之后才来找你请求下一步指示

**我们正在进入「后提示词」时代**。

### 命名渊源与调侃

- OpenClaw 原名叫 **Clawdbot**，被 Anthropic 勒令改名（怀疑蹭 Claude 热度）
- 文章调侃：Anthropic 现在自研产品问世，可能会重新启用 "Clawdbot" 这个名字

### 关键引用

**Karpathy**:
> "这完全就是他预言中 AI 的下一个进化方向。一个『龙虾版』的 Claude Code。"

**量子位总结**:
> "AI 的下一步已经很明确了。进入应用时代，模型能力的释放，需要更多的背景信息，和更高的权限。"

**Ole Lehmann**:
> "我真不敢相信，这事儿居然没人讨论！"

### 参考链接

- Karpathy 推文: https://x.com/karpathy/status/2039057005802082814
- Ole Lehmann 分析: https://x.com/itsolelehmann/status/2039018963611627545

---

*创建时间: 2026-04-01*
*素材更新时间: 2026-04-01*
