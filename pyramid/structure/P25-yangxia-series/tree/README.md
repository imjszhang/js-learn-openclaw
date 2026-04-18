# 金字塔全树

> 所属视角：P25 - JS 的养虾日记（公众号系列）

从 [scqa.md](../scqa.md) 的塔尖结论出发，逐层向下展开。

## 塔尖

通过 37 天真实"养虾"记录，展示从养出第一只社交龙虾 JS_BestAgent 到构建出包含五个项目的 AI 工具小生态的完整路径——不是教程，是一个普通人从"装完发呆"到"离不开它"的成长日记。

## Key Line（顶层论点）

**KL01-KL06 系列**：从"能干什么"到"为什么本地部署"到"浏览器自动化"到"新手 20 条"到"知识收集器"再到"知识棱镜"，层层深入。

| 序号 | 论点                                                       | 逻辑顺序 | 复用 P23 KL                   | 引用 Synthesis              | 状态      | 引用 Groups                     | 展开文件                    |
| ---- | ---------------------------------------------------------- | -------- | ----------------------------- | --------------------------- | --------- | ------------------------------- | --------------------------- |
| KL01 | 我养了两只 AI 龙虾——通过它们的日常，看 OpenClaw 能干什么   | 时间     | KL03,KL04,KL06,KL11,KL18,KL19 | S15,S12,S32,S35,S23,S33,S22 | ✅ 已完成 | G21,G18,G51,G34,G52,G33,G50,G53 | KL01-two-lobsters.md        |
| KL02 | 掌控 AI，而不是畏惧 AI——四个问题，看懂为什么必须本地部署   | 递进     | KL13,KL16,KL19                | S4,S9,S27,S28,S30,S31       | ✅ 已完成 | G07,G11,G39,G40,G41,G43         | KL02-master-not-fear.md     |
| KL03 | 给 OpenClaw 装上眼睛：JS-Eyes 解决登录态难题               | 工具     | KL34,G64                      | S23,S45                     | ✅ 已完成 | G34,G64                         | KL03-js-eyes-login.md       |
| KL04 | OpenClaw 新手起飞：20 条救命要点，看完直接上手             | 清单     | KL01(新手教程)                | S7,S30                      | 🆕 已创建 | G12,G35                         | KL04-newbie-checklist.md    |
| KL05 | 知识收集器是 AI 时代的"感官系统"——让龙虾能看、能记、能思考 | 系统     | KL17,KL18,KL19                | S27,S45                     | ✅ 已完成 | G06,G07,G08,G09,G10             | KL05-knowledge-collector.md |
| KL06 | 知识棱镜是 AI 时代的"消化系统"——把信息洪流变成可复用的洞察 | 系统     | KL10,KL21,KL22,KL23,KL24,KL25 | S27,S45,S49                 | 🆕 已创建 | G27,G49,G55,G56,G57,G58,G59,G60 | KL06-knowledge-prism.md     |
| KL07 | App 消亡论：我的 OpenClaw 没有界面，因为它本身就是界面     | 哲学     | KL34,KL35                     | S23,S45,S49                 | 🆕 已创建 | G34,G50,G52,G64                 | KL07-app-disappears.md      |
| KL08 | 腾讯抢入口：微信接入 OpenClaw 的战略意义                   | 热点     | KL02,KL07                     | S4,S23,S27,S45              | 🆕 已创建 | G07,G40,G43,G50,G52,G64         | KL08-wechat-entrance.md     |
| KL09 | 别把龙虾当工具，把它当员工——Harness Engineering 四层架构   | 系统     | KL02,KL07                     | S4,S23,S27,S45              | ✅ 已完成 | G77,G78,G50,G52,G64             | KL09-harness-engineering.md |
| KL10 | 装好了 OpenClaw 不知道干什么？先做这四件事                 | 指南     | KL01,KL04,KL05,KL06,KL08,KL09 | S4,S27,S45                  | 🆕 已创建 | G07,G09,G27,G50,G77,G78         | KL10-four-things.md |
| KL11 | 使用龙虾 OpenClaw 干活的正确姿势：和用 Deepseek 完全不同   | 实战     | KL09                          | S27,S45                     | 🆕 已创建 | G80,G77,G78                     | KL11-hackthon-recruit-design.md |
| KL12 | 龙虾写代码太烧钱？教它学会「外包」——一条命令省下 90% Token | 成本     | KL09,KL11                     | S62,S49                     | 🆕 已创建 | G81                             | KL12-acp-outsourcing.md |
| KL13 | OpenClaw 好用的关键：配置好记忆 | 系统 | KL05,KL06,KL09 | S27,S45 | 🆕 已创建 | G41,G52,G57 | KL13-three-layer-memory.md |
| KL14 | 让龙虾自动盯盘——X.com 动态监控实战 | 实战 | KL03 | S23,S45 | ✅ 已完成 | G34,G64 | KL14-x-monitoring.md |
| KL15 | 从 Claude Code 源码学习 Harness Engineering 配置 | 热点 | KL09,KL11,KL12,KL13 | S27,S45,S49,S62 | ✅ 已完成 | G07,G34,G41,G50,G52,G57,G64,G77,G78,G81 | KL15-learn-from-claude-code.md |
| KL16 | 教你的龙虾一套知识管理方法论 | 系统 | KL05,KL06,KL13 | S27,S45 | ✅ 已完成 | G06,G07,G27,G49,G55,G56,G57,G58,G59,G60 | KL16-knowledge-management-method.md |
| KL17 | 从堆积到生长：我用 OpenClaw 建了一个会思考的资料库 | 实战 | KL05,KL06,KL13,KL16 | S27,S45 | 🆕 已创建 | G27,G49 | KL17-prism-in-practice.md |
| KL18 | 与 OpenClaw 最搭的本地生图模型：Z-Image Turbo | KL09,KL11,KL15,KL26 | S45,S61 | ✅ 已发布 | G34,G73,G80 | KL18-comfyui-local-image-generation.md |
| KL19 | 把 Karpathy 的知识库思路，嫁接进我的养虾系统 | 热点 | KL05,KL06,KL13,KL16 | S27,S45 | ✅ 已发布 | G27,G49 | KL19-karpathy-prism-fusion.md |
| KL20 | 从 OpenClaw 官方文档里了解的时间哲学：工业时间与生物时间 | 轻松好玩 | KL09,KL13,KL16 | S27,S45 | ✅ 已发布 | - | KL20-four-levels-of-delegation.md |
| KL21 | 我用龙虾管理微信收藏的900篇文章 | 痛点共鸣 | KL05,KL06,KL13,KL16 | S27,S45 | ✅ 已发布 | - | KL21-wechat-favorites-900.md |
| KL22 | 一人顶三人：我在 OpenClaw 里搭了一个最小化团队 | 解决痛点 | KL09,KL11,KL12,KL13,KL14,KL20 | S27,S45 | ✅ 已发布 | G34,G64,G81 | KL22-one-man-team.md |
| KL23 | Claude Code 被"开源"了，我为什么还在用 OpenClaw？ | 疑问新知 | KL09,KL13,KL15 | S27,S45,S62 | 🆕 已创建 | - | KL23-art-of-delegation.md |
| KL24 | 我的 OpenClaw 数字分身？不，我只是让它记住了我是谁 | 轻松好玩 | KL13,KL16 | S27,S45 | 🆕 待写 | - | - |
| KL25 | 当所有人都在用 ChatGPT，我为什么选择 OpenClaw | 解决痛点 | KL02,KL07,KL09 | S4,S23,S27,S45 | 🆕 待写 | - | - |
| KL26 | 爱马仕取代龙虾？4 万星的 Hermes 到底在证明什么 | 疑问新知 | KL02,KL09,KL13,KL17,KL25 | S27,S45,S62 | ✅ 已发布 | G95,G77,G83 | KL26-hermes-proves-the-path.md |
| KL27 | 发现个新开源工具，帮助龙虾彻底接管微信 | 疑问新知 | KL21,KL25 | - | ✅ 已完成 | - | KL27-wechat-cli-knowledge-graph.md |
| KL28 | JS-Eyes 2.0：从浏览器工具到技能生态——我的龙虾现在能自动发现新能力了 | 解决痛点 | KL03,KL05,KL06,KL27 | - | ✅ 已完成 | - | KL28-js-eyes-2-skill-ecosystem.md |
| KL29 | 现在养虾，就是 13 年做微信公众号 | 盲区纠正 | KL01,KL02,KL09,KL28 | - | ✅ 已完成 | - | KL29-lobster-is-2026-wechat-moment.md |
| KL30 | 你的知识系统，不用你维护了 | 解决痛点 | KL05,KL06,KL13,KL16,KL17,KL21 | S27,S45,S49 | ✅ 已完成 | G06,G07,G27,G49 | KL30-knowledge-system-growth.md |
| KL31 | 教你的龙虾，用你的flomo | 解决痛点 | KL05,KL06,KL16,KL27,KL30 | - | ✅ 已完成 | - | KL31-notes-reading-system.md |
| KL32 | 我的龙虾成了我的第二大脑 | 解决痛点 | KL05,KL27,KL30,KL31 | - | 🆕 已创建 | - | KL32-personal-brain.md |

## 结构检查

- [x] KL01 已详细展开，有 atom 引用支撑
- [x] KL02 已完成展开（四个问题递进结构：信任→边界→代价→未来）
- [x] KL03 骨架已创建（登录态复用痛点 +9 个 AI 工具）
- [x] KL04 已创建（基于 2 万字教程反向制作，20 条清单式结构）
- [x] KL08 已创建（微信接入战略分析：教程 + 隐私 + 生态 + 机会）
- [x] KL11 已创建（三层结构：记忆/技能/协作，Deepseek 对比锚点，js-comfyui-skill 开源分享）
- [x] KL12 已创建（三层结构：成本认知/外包方案/一条命令激活，承接 KL09 员工隐喻，js-cursor-agent 开源分享）
- [x] KL13 已创建（三层记忆架构：原生记忆/Prism/Collector 协同）

> 注：
>
> - KL02 聚焦"本地部署 vs 云端风险"，从信任→边界→代价→未来四层递进
> - KL03 聚焦"浏览器自动化"，解决公众号/小红书/知乎/即刻等登录墙问题
> - KL04 聚焦"新手入门"，将 2 万字教程压缩为 20 条救命要点

---

## 修订记录

| 日期       | 变更摘要                                                                         |
| ---------- | -------------------------------------------------------------------------------- |
| 2026-04-18 | 新增 KL32「我在 OpenClaw 里养了一个第二大脑」，collector+flomo+prism 三合一私人智库，龙虾进化到"完整的人"，6 个环节结构 |
| 2026-04-17 | 新增 KL31「教你的龙虾，用你的flomo」，基于 js-knowledge-flomo 项目调研，7 个 AI 洞察工具，从"搜索"到"发现"的认知升级，对标 KL27 叙事结构 |
| 2026-04-06 | KL21 标题重写为"我用龙虾管理微信收藏的900篇文章"，从"避坑指南"转向"痛点共鸣"，围绕 js-knowledge-collector 900+微信文章管理实战，新增 KL21-wechat-favorites-900.md 框架 |
| 2026-04-05 | 新增 KL22"一人顶三人"框架，基于真实自动化工作流（x-monitor + link-collector + prism + ACP），展示从"执行者"到"指挥者"的角色转变，引用 G34/G64/G81 |
| 2026-04-04 | 新增 KL20"委托的四个层级"，基于 OpenClaw 文档深度研究（cron vs heartbeat vs hooks），展示从手动到全自动的委托进化路径 |
| 2026-04-12 | KL26 重写：从"Anthropic 不开放 Mythos API"改为"Hermes 4 万星架构拆解"，核心叙事从"模型同质化"转为"Hermes 证明龙虾方向是对的"，文件更名为 KL26-hermes-proves-the-path.md |
| 2026-04-15 | KL29 结构重构：7 KA 合并为 4 条逻辑线，标题改为"为什么我不看好 2026 年做公众号，但我看好养虾"，学 kzk 时间线叙事 + 类比贯穿，类别从"疑问新知"改为"盲区纠正" |
| 2026-04-14 | KL29 正文创作完成：以 2012 公众号类比 2026 养虾，揭示"做自媒体不是资产，养虾才是"，三列对比表破局 |
| 2026-04-16 | KL30 创作完成：标题「你的知识系统，不用你维护了」，以传统知识管理三死穴切入，对比 Notion 仓库 vs 龙虾生态，诚实账本不夸大产出，引用 G06/G07/G27/G49/S27/S45/S49 |
| 2026-04-16 | KL18 状态更新为已发布（ComfyUI 本地生图），文件更名为 KL18-comfyui-local-image-generation.md |
| 2026-04-14 | KL28 创作完成：JS-Eyes 2.0 单一主插件架构升级，从"装什么会什么"到"看到什么学什么"，龙虾能力边界彻底打开 |
| 2026-04-13 | KL27 正文创作完成：从"你喂龙虾"到"龙虾自己拿"的进化，引入 wechat-cli 作为微信数据出口，规约同步更新（§4/§5/§6.8） |
| 2026-04-04 | KL19 状态更新为"已发布"；补全 KL20-26 待写选题到表格；调整 KL18 为预留 |
| 2026-04-04 | 新增 KL19"把 Karpathy 的知识库思路，嫁接进我的养虾系统"，基于 Karpathy 最新文章分析，与 prism 对比融合，引用 G27/G49/S27/S45 |
| 2026-04-04 | KL16 状态更新为已完成，KL17 已创建；KL18 预留；调整 KL16-KL18 状态 |
| 2026-03-31 | 新增 KL14"让龙虾自动盯盘"，基于 js-eyes X.com 监控实战，数据收集中，引用 G34/G64/S23/S45 |
| 2026-03-29 | 新增 KL13"龙虾的三层记忆"，基于三层记忆架构（原生记忆/Prism/Collector），引用 G41/G52/G57/S27/S45 |
| 2026-03-27 | 新增 KL12"龙虾写代码太烧钱？教它学会「外包」"，基于 ACP + Cursor Agent 集成日志，三层结构（成本认知/外包方案/一条命令激活），引用 G81/S62 |
| 2026-03-25 | KL11 重写标题为"使用龙虾 OpenClaw 干活的正确姿势：和用 Deepseek 完全不同"，三层结构（记忆/技能/协作），新增 js-comfyui-skill 开源分享段，Groups 更新为 G80,G77,G78 |
| 2026-04-01 | KL15 已完成（Claude Code 源码泄露热点回应，六大技术/八个 Skill 映射到 OpenClaw 配置） |
| 2026-03-22 | KL08 已创建（微信接入 OpenClaw 战略分析：教程 + 隐私 + 生态 + 机会四层结构）     |
| 2026-03-12 | 聚焦单篇爆文策略，移除系列预告，仅 KL01 一篇                                     |
| 2026-03-09 | 首次构建，8 条 KL 概览，仅 KL01 详细展开，其余骨架待读者反馈后推进               |
| 2026-03-09 | KL01 从"ChatGPT 对比科普"重写为"两只龙虾的故事"，文件更名为 KL01-two-lobsters.md |
