# js-knowledge-flomo：flomo 知识洞察系统

> 日期：2026-04-17
> 项目：js-knowledge-flomo
> 类型：系统介绍 / 功能实现
> 来源：项目调研 + 养虾日记 KL31 选题

---

## 1. 背景与动机

KL30（你的知识系统，不用你维护了）解决了知识库自动维护的问题——Prism 自动拆解、归组、产出。

但还有一个更深层的痛点：**flomo 里的笔记，记了但不读。**

flomo 用户的核心困境不是"没记"，是"记太多读不过来"：
- 搜索一个关键词，跳出来 200 条结果，然后关掉窗口
- 知道自己写过某个东西，但不记得具体写了什么
- 笔记之间可能有隐藏关联，但人脑无法同时处理

**KL27 解决的是"龙虾够不到数据"（微信数据库），KL31 要解决的是"龙虾读不完笔记"。**

除非——系统替你先读，读完了告诉你它发现了什么。

---

## 2. 项目概览

| 维度 | 内容 |
|------|------|
| **项目名** | js-knowledge-flomo |
| **定位** | 以 flomo 为核心的知识管理工具 — MCP Client + MCP Server 双角色 |
| **GitHub** | github.com/imjszhang/js-knowledge-flomo |
| **许可证** | MIT |
| **技术栈** | Node.js 18+ / SQLite 缓存 / OpenAI 兼容 API / flomo 官方 MCP |

### 核心架构

```
js-knowledge-flomo/
├── cli/                         # CLI 入口 + 核心逻辑
│   ├── cli.js                   # auth/search/write/insights...
│   └── lib/
│       ├── auth.js              # flomo OAuth 授权
│       ├── database.js          # SQLite 本地缓存
│       ├── flomo-mcp-client.js  # flomo MCP 客户端
│       ├── llm.js               # LLM API 调用
│       ├── processor.js         # AI 加工引擎（7 个分析功能）
│       └── server.js            # Web UI 服务
├── mcp-server/                  # MCP Server
│   ├── index.mjs
│   └── tools/
│       ├── ai-tools.js          # AI 分析工具
│       ├── memo-tools.js        # 笔记读写工具
│       └── tag-tools.js         # 标签管理工具
├── openclaw-plugin/             # OpenClaw 插件
│   └── skills/flomo-assistant/
│       └── SKILL.md
├── prompts/                     # AI 提示词模板
│   ├── insight.txt              # 洞察分析
│   ├── relate.txt               # 关联发现
│   ├── outline.txt              # 写作大纲
│   ├── tag-suggest.txt          # 标签建议
│   └── digest.txt               # 摘要
└── src/                         # Web UI 静态资源
```

---

## 3. AI 分析功能（7 个维度）

### 3.1 洞察发现（generateInsights）

**问题**：我记了这么多，到底在关心什么？

**原理**：
1. 按标签/时间范围搜索笔记（默认 50 条）
2. 笔记缓存到 SQLite
3. 用 prompt 调用 LLM 分析三个维度：反复主题、情绪变化、隐藏关联
4. 洞察结果保存到数据库，支持追溯

**Prompt 核心要求**：
- 反复主题：哪些话题/关键词反复出现？说明了什么？
- 情绪与态度变化：笔记中的语气和立场是否有变化趋势？
- 未被注意的关联：不同时间、不同标签下的笔记之间，是否存在用户可能没注意到的关联？
- 最后总结"你最近真正在关心什么"

### 3.2 关联发现（findConnections）

**问题**：这些笔记之间有什么关系？

**原理**：
1. 获取笔记（可限定标签）
2. LLM 跨标签/跨时间找关联
3. 至少找出 3 组有意义的连接
4. 推测"你可能在思考的大问题是什么"

**Prompt 核心要求**：
- 不同领域的笔记之间是否存在共同的底层逻辑
- 早期的想法是否在后来得到了验证或推翻
- 看似无关的笔记是否在解决同一个问题

### 3.3 观点演变（trackEvolution）

**问题**：我对这个问题的看法是怎么变的？

**原理**：
1. 按关键词搜索相关笔记
2. LLM 按时间线标注关键转折点
3. 指出观点从 A 变成 B 的证据
4. 总结当前最新立场

### 3.4 写作大纲（draftOutline）

**问题**：我想写个东西，素材都在这但怎么组织？

**原理**：
1. 按主题搜索笔记（默认 30 条）
2. LLM 从散落笔记生成结构化大纲
3. 基于真实素材，不是凭空生成

### 3.5 素材搜集（collectMaterial）

**问题**：我要写 X 主题，相关笔记有哪些？

**原理**：
1. 按主题搜索笔记
2. 对前 5 条笔记调用 flomo 的"相关推荐"接口
3. 合并去重，返回直接匹配 + 关联推荐

### 3.6 标签建议（suggestTags）

**问题**：笔记没分类，懒得一条条打标签

**原理**：
1. 获取现有标签树
2. 筛选无标签笔记
3. LLM 根据标签树为无标签笔记推荐标签

### 3.7 标签审计（auditTags）

**问题**：标签越打越乱，怎么优化？

**原理**：
1. 获取完整标签树
2. LLM 分析四个维度：重复标签、命名不一致、层级问题、缺失建议
3. 给出具体操作建议（合并、重命名、移动等）

---

## 4. 三种使用方式

| 方式 | 入口 | 适用场景 |
|------|------|----------|
| **OpenClaw 插件** | `openclaw flomo <命令>` | 在龙虾里直接对话式使用，15 个 flomo 工具 |
| **独立 CLI** | `node cli/cli.js <命令>` | 终端命令行，独立运行不依赖 OpenClaw |
| **MCP Server** | Cursor / Claude Desktop | 在 IDE 里当 MCP 工具调用 |
| **Web UI** | `npm run dev` → http://127.0.0.1:3000 | 本地网页，搜索/标签树/洞察生成/统计 |

### OpenClaw 插件工具列表（15 个）

**笔记操作**：
- `flomo_memo_create` — 创建笔记（支持 #标签）
- `flomo_memo_update` — 更新已有笔记
- `flomo_memo_search` — 搜索笔记
- `flomo_memo_batch_get` — 批量获取笔记详情
- `flomo_memo_recommended` — 获取相关推荐笔记

**标签操作**：
- `flomo_tag_tree` — 获取完整标签层级
- `flomo_tag_search` — 搜索标签
- `flomo_tag_rename` — 重命名标签

**AI 分析**：
- `flomo_generate_insights` — 分析笔记中的规律和隐藏模式
- `flomo_track_evolution` — 追踪某话题的观点演变
- `flomo_find_connections` — 发现跨标签、跨时间的隐藏关联
- `flomo_draft_outline` — 从笔记生成写作大纲
- `flomo_collect_material` — 搜集主题相关笔记素材
- `flomo_suggest_tags` — 为无标签笔记推荐标签
- `flomo_audit_tags` — 分析标签体系问题并给出优化建议

---

## 5. 核心设计决策

| 决策 | 选择 | 理由 |
|------|------|------|
| flomo 接入方式 | 官方 MCP 协议 | OAuth 授权，安全标准，不需要逆向 |
| 本地缓存 | SQLite | 轻量、零依赖、支持 SQL 查询 |
| AI 分析 | 走用户自建 LLM API | 数据不出用户环境，隐私安全 |
| 架构设计 | MCP Client + MCP Server 双角色 | 同时支持 CLI/插件/IDE 三种用法 |
| 提示词管理 | 独立 prompts 目录 | 可单独优化，不耦合代码 |

---

## 6. 与养虾日记系列的呼应

| 前文 | 呼应点 |
|------|--------|
| KL05 | 收集袋——能看能记 |
| KL06 | 棱镜——能消化 |
| KL21 | 微信收藏 900 篇——堆积变生长 |
| KL27 | wechat-cli——龙虾自己去拿 |
| KL30 | 不用维护——系统自生长 |
| KL31 | **笔记全读——系统替你思考** |

---

## 7. 文章叙事线

```
KL27: 微信数据龙虾够不到 → wechat-cli 帮龙虾拿到钥匙 → 龙虾自己去拿
KL31: flomo笔记人读不完   → js-knowledge-flomo 让笔记自己读自己 → 系统替你思考
```

**文章标题**：《教你的龙虾，用你的flomo》

**核心金句候选**：
- "你不用再怕笔记越记越多了。因为你不需要读了。龙虾会先读。"
- "从搜索到发现——系统告诉你你没想到的东西。"
- "记了不读 = 白记。除非系统替你先读。"
