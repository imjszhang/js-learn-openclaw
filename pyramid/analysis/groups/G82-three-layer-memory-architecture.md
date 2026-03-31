# G82: 三层记忆系统通过"原生即时 - 结构化沉淀 - 外部情报"的有机协同，构建了可控、可追溯且具备时间复利的认知外挂

> 市面上的 AI 记忆方案因黑箱不可控或缺乏分层而失效，唯有将短期会话、结构化知识与外部情报三层解耦又协同，才能形成随时间增值的数字资产。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| LM-01 | kl13-three-layer-memory  | 市面上的 AI 记忆方案存在黑箱不可控、需手动上传或缺少分层等局限性 |
| LM-02 | kl13-three-layer-memory  | 三层记忆系统由原生记忆、Knowledge Prism 和 Collector 三个有机协同的层级组成 |
| LM-03 | kl13-three-layer-memory  | 原生记忆层载体为 MEMORY.md 及 memory 目录下的文件，用于存储短期会话、个人决策和每日日志 |
| LM-04 | kl13-three-layer-memory  | 原生记忆层具有即时加载、人工筛选精确性和高度个人化的特点 |
| LM-05 | kl13-three-layer-memory  | Knowledge Prism 层负责将 Journal 笔记提炼为 Atoms，归组为 Groups，并收敛为 Synthesis |
| LM-06 | kl13-three-layer-memory  | Knowledge Prism 的处理流程为：Journal → Atoms → Groups → Synthesis → Perspectives → Outputs |
| LM-07 | kl13-three-layer-memory  | Collector 层通过 js-knowledge-collector 数据库抓取并自动总结外部文章（如公众号、GitHub 等） |
| LM-08 | kl13-three-layer-memory  | Collector 层已积累 1289+ 篇外部文章，每篇均包含 AI 生成的概要、摘要和推荐理由 |
| LM-09 | kl13-three-layer-memory  | 回答技术问题时，先检索原生记忆定位讨论时间，再检索 Prism 获取相关 Groups，最后判断是否引用外部情报 |
| LM-10 | kl13-three-layer-memory  | 撰写新文章时，融合原生记忆中的配置细节、Prism 中的结构化 Groups 以及 Collector 中的外部对比素材 |
| LM-11 | kl13-three-layer-memory  | 三层记忆系统相比其他方案，在可控性、可追溯性、个人化、结构化和外部情报集成上具有显著优势 |
| LM-12 | kl13-three-layer-memory  | 三层记忆系统的核心价值在于时间复利，随时间积累形成普通 AI 无法复制的数字资产 |
| LM-13 | kl13-three-layer-memory  | 该系统不仅是存储工具，更是认知外挂，分别充当第二大脑、知识消化器和情报网络 |
| LM-14 | kl13-three-layer-memory  | 原生记忆层配置需开启 hybrid 模式，采用 70% 向量搜索与 30% BM25 关键词搜索的混合策略 |
| LM-15 | kl13-three-layer-memory  | 原生记忆层重排序策略采用 MMR（lambda=0.7）结合 30 天半衰期的时间衰减算法 |
| LM-16 | kl13-three-layer-memory  | Prism 层通过只导出高价值层级和基于 mtime 的增量同步，可降低 80% 的文件数量 |
| LM-17 | kl13-three-layer-memory  | Prism 层自动化管线包含提取 Atoms/Groups、生成检索索引，并通过 Cron 定时执行无需人工干预 |
| LM-18 | kl13-three-layer-memory  | Collector 层采用 inbox/batch 轮转架构及专用 scraper，支持微信、知乎、GitHub 等八大平台 |
| LM-19 | kl13-three-layer-memory  | 知识系统的有效记忆依赖于"消化"而非单纯"存储"，需像土壤一样自我生长 |
| LM-20 | kl13-three-layer-memory  | 文章切入点应通过对比测试展示三层架构如何解决"记不住"的痛点，并预埋关于记忆本质的金句 |

## 组内逻辑顺序

采用"问题痛点 - 架构定义 - 分层详解 - 协同场景 - 技术实现 - 核心价值"的结构顺序。
1. **痛点与总览** (LM-01, LM-02)：指出市面方案缺陷，提出三层架构概念。
2. **分层详解** (LM-03~LM-08)：依次介绍原生记忆层、Prism 层、Collector 层的定义与功能。
3. **协同场景** (LM-09, LM-10)：描述三层在回答问题与写作时的具体协作流程。
4. **优势与价值** (LM-11~LM-13, LM-19)：总结系统优势、时间复利价值及"消化"理念。
5. **技术实现** (LM-14~LM-18)：详述各层的搜索策略、重排序算法、自动化管线及数据架构。
6. **落地建议** (LM-20)：关于内容创作与痛点展示的建议。
