# G102: 知识洞察系统必须从"被动记录"进化为"主动思考"，通过 LLM 自动挖掘笔记间的隐藏关联与观点演变

> 解决 flomo 用户"笔记读不完"的核心痛点，利用 MCP 架构与本地缓存构建自动化分析引擎，实现从数据积累到认知升维的跨越。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| KN-01 | kl31-notes-reading-system | flomo 用户的核心痛点不是记录不足，而是笔记过多导致无法阅读和消化 |
| KN-02 | kl31-notes-reading-system | KL31 项目的定位是解决"笔记读不完"的问题，通过系统自动分析代替人工阅读 |
| KN-03 | kl31-notes-reading-system | js-knowledge-flomo 项目同时扮演 MCP Client 和 MCP Server 双角色 |
| KN-04 | kl31-notes-reading-system | 项目技术栈包含 Node.js 18+、SQLite 本地缓存、OpenAI 兼容 API 及 flomo 官方 MCP |
| KN-05 | kl31-notes-reading-system | 核心架构由 CLI 入口、MCP Server、OpenClaw 插件、Prompts 模板目录及 Web UI 静态资源组成 |
| KN-06 | kl31-notes-reading-system | 洞察发现功能通过搜索笔记、缓存至 SQLite、调用 LLM 分析主题/情绪/关联、保存结果四步实现 |
| KN-07 | kl31-notes-reading-system | 洞察分析 Prompt 需涵盖反复主题、情绪变化趋势及跨时间/标签的隐藏关联三个维度 |
| KN-08 | kl31-notes-reading-system | 关联发现功能要求 LLM 跨标签跨时间寻找至少 3 组有意义的连接并推测用户思考的大问题 |
| KN-09 | kl31-notes-reading-system | 观点演变功能通过按关键词搜索、LLM 标注时间线转折点、指出观点变化证据来追踪立场变化 |
| KN-10 | kl31-notes-reading-system | 写作大纲功能基于真实散落笔记生成结构化大纲，而非凭空生成 |
| KN-11 | kl31-notes-reading-system | 素材搜集功能先按主题搜索，再对前 5 条笔记调用 flomo"相关推荐"接口进行合并去重 |
| KN-12 | kl31-notes-reading-system | 标签建议功能通过获取现有标签树，利用 LLM 为无标签笔记自动推荐标签 |
| KN-13 | kl31-notes-reading-system | 标签审计功能分析标签树的重复、命名不一致、层级问题及缺失维度，给出合并或重命名建议 |
| KN-14 | kl31-notes-reading-system | 系统提供 OpenClaw 插件、独立 CLI、MCP Server (IDE) 及本地 Web UI 四种使用方式 |
| KN-15 | kl31-notes-reading-system | OpenClaw 插件集成了 15 个工具，涵盖笔记 CRUD、标签管理及 7 项 AI 分析功能 |
| KN-16 | kl31-notes-reading-system | 选择 flomo 官方 MCP 协议接入是为了利用 OAuth 授权确保安全性，避免逆向工程 |
| KN-17 | kl31-notes-reading-system | 选用 SQLite 作为本地缓存是因为其轻量、零依赖且支持 SQL 查询的特性 |
| KN-18 | kl31-notes-reading-system | AI 分析走用户自建 LLM API 是为了确保数据不出用户环境，保障隐私安全 |
| KN-19 | kl31-notes-reading-system | 将 Prompt 模板独立存放在 prompts 目录中，便于单独优化且不与代码耦合 |
| KN-20 | kl31-notes-reading-system | 该项目是养虾日记系列中 KL31 选题的实现，标志着从"系统自生长"进化到"系统替你思考" |

## 组内逻辑顺序

采用"问题定位 - 架构设计 - 核心功能 - 安全隐私 - 演进意义"的结构顺序：首先明确痛点与项目定位 (KN-01~02)，接着阐述技术栈与整体架构 (KN-03~05)，随后详细展开 7 项核心 AI 分析功能 (KN-06~13) 及多端使用方式 (KN-14~15)，再说明安全与隐私设计原则 (KN-16~19)，最后总结其在养虾日记系列中的演进意义 (KN-20)。
