# G88: 知识库架构需融合"Karpathy 轻量全量读取”与"Prism 深度蒸馏”双模式，以适配个人管理与内容产出的差异化场景

> 不同规模与目标的知识库应采用差异化架构：个人研究适用"raw 采集 +LLM 全量读取 + 自动健康检查”的轻量闭环，而内容创作需保留"Journal-Pyramid-Outputs"的深度蒸馏流程，两者可通过混合模式实现能力互补。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| KK-01 | kl19-karpathy-prism-fusion | Karpathy 知识库核心架构包含 raw/统一采集、LLM 自动编译 Wiki、中等规模全量读取及自动健康检查机制 |
| KK-02 | kl19-karpathy-prism-fusion | 中等规模知识库（约 100 篇/40 万词）无需引入 RAG 架构，直接全量读取即可满足需求 |
| KK-03 | kl19-karpathy-prism-fusion | js-knowledge-collector 已实现链接采集、AI 总结、入库及 Flomo 推送的一体化流程 |
| KK-04 | kl19-karpathy-prism-fusion | collector 不仅是采集器，实质上是“采集 + 自动编译”的一体化工具，领先于 Karpathy 的纯采集方案 |
| KK-05 | kl19-karpathy-prism-fusion | Karpathy 路线相比 Prism 路线多出了全自动索引维护、健康检查以及“提问→归档”的闭环机制 |
| KK-06 | kl19-karpathy-prism-fusion | Karpathy 路线适用于研究员个人知识管理（轻量快速），Prism 路线适用于内容创作者知识生产线（深度蒸馏） |
| KK-07 | kl19-karpathy-prism-fusion | 不同知识库路线无绝对优劣之分，核心差异在于适用场景是个人管理还是内容产出 |
| KK-08 | kl19-karpathy-prism-fusion | 融合方案之一是增加「轻量模式」：针对小库让 journal 直接对接 LLM 问答，跳过 atoms/groups 中间层 |
| KK-09 | kl19-karpathy-prism-fusion | 融合方案之二是新增 `knowledge_prism_health_check` 功能，用于发现孤立 atoms、矛盾陈述及选题 gaps |
| KK-10 | kl19-karpathy-prism-fusion | 融合方案之三是建立“提问→归档”循环，将对话中发现的洞察经授权后自动写回 journal |
| KK-11 | kl19-karpathy-prism-fusion | 融合方案之四是扩展输出类型，支持 slides（Marp）和 diagram（Mermaid）等多模态格式 |
| KK-12 | kl19-karpathy-prism-fusion | KL19 文章的核心结论是 collector 已领先一步，prism 需简化流程并杂交出适合创作者的版本 |
| KK-13 | kl19-karpathy-prism-fusion | KL19 文章框架承接了 KL05-06 的基础设施讨论、KL13 的记忆系统配置及 KL15 的 Harness 讨论 |

## 组内逻辑顺序

采用"现状对比→场景定位→融合策略→演进结论"的逻辑顺序：
1. KK-01~KK-05：分析 Karpathy 架构特点及其与现有 Prism/Collector 方案的差异对比。
2. KK-06~KK-07：明确两种架构的适用场景边界（个人研究 vs 内容创作）。
3. KK-08~KK-11：提出具体的四种融合实施方案（轻量模式、健康检查、闭环归档、多模态输出）。
4. KK-12~KK-13：总结核心结论及文章在整体知识体系中的承上启下关系。
