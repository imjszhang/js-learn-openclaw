# G99: 微信群聊"暗知识"挖掘需构建"本地内存解密 -LLM 实体提取 - 图谱可视化"的零信任自动化链路

> 利用纯本地工具扫描进程内存提取微信加密数据，结合 LLM 语义分析与图谱算法，将高价值但易丢失的群聊对话转化为可检索的结构化资产。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| WK-01 | kl27-wechat-cli-knowledge-graph | 微信群聊中的产品决策、技术选型和项目共识属于未被文档化的"暗知识"，具有极高价值但易丢失 |
| WK-02 | kl27-wechat-cli-knowledge-graph | wechat-cli 是一个纯本地、只读、零网络的开源工具，通过扫描进程内存提取 AES-256 密钥来解密微信数据库 |
| WK-03 | kl27-wechat-cli-knowledge-graph | wechat-cli 核心仅由 320 行 C 代码构成，依赖 click、pycryptodome 和 zstandard 三个 Python 库 |
| WK-04 | kl27-wechat-cli-knowledge-graph | wechat-cli 主要支持 macOS 平台，提供 sessions、history、export 等 11 条命令行操作 |
| WK-05 | kl27-wechat-cli-knowledge-graph | Graphify 利用 LLM 提取实体并构建关系，通过 Leiden 算法进行社区聚类，最终生成可交互的知识图谱 |
| WK-06 | kl27-wechat-cli-knowledge-graph | Graphify 的标准输入为 Markdown 文件，输出包含 graph.html、GRAPH_REPORT.md 和 graph.json |
| WK-07 | kl27-wechat-cli-knowledge-graph | 完整数据链路为：微信加密 SQLite → wechat-cli 内存解密 → 导出带元数据的 Markdown → Graphify 生成图谱 |
| WK-08 | kl27-wechat-cli-knowledge-graph | 可通过 cron 定时任务配合 wechat-cli 的 new-messages 命令实现群聊知识的每日自动增量同步 |
| WK-09 | kl27-wechat-cli-knowledge-graph | 将叙事角度定位为"暗知识挖掘"比纯工具教程更能引发读者共鸣，使其联想到自身群聊价值 |
| WK-10 | kl27-wechat-cli-knowledge-graph | 针对知识管理用户而非安全工程师时，技术深度应控制在提供命令和原理层面，无需展开 C 代码细节 |
| WK-11 | kl27-wechat-cli-knowledge-graph | 由于涉及内存扫描和 root 权限，文章必须包含明确的安全与合规提醒 |
| WK-12 | kl27-wechat-cli-knowledge-graph | 必须明确注明工具仅支持 macOS 平台，以避免 Windows 用户尝试时踩坑 |
| WK-13 | kl27-wechat-cli-knowledge-graph | KL21 项目处理的是微信收藏的"明知识"（文章链接），而本项目处理的是群聊的"暗知识"（对话内容），两者形成互补 |
| WK-14 | kl27-wechat-cli-knowledge-graph | 知识图谱相比传统文章库的优势在于支持语义检索和社区聚类，能将碎片化聊天转化为结构化资产 |

## 组内逻辑顺序

按"问题定义 (暗知识价值) → 技术原理 (内存解密) → 工具实现 (wechat-cli/Graphify) → 数据链路 → 自动化运维 → 内容策略与安全合规 → 差异化定位"的逻辑排列。
