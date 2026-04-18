# JS-Eyes 2.0 架构升级：从子插件地狱到单一主插件 + 自动发现

> 日期：2026-04-14
> 项目：js-eyes
> 类型：架构设计 / 升级迁移
> 来源：GitHub Releases + 本地代码调研

---

## 1. 背景与动机

KL03（养虾日记第 3 篇）写过给龙虾装眼睛的故事。从那时起，JS-Eyes 从单纯的"浏览器登录态复用工具"长成了一个技能生态：X.com 搜索、Bilibili 视频、小红书笔记、知乎回答、微信公众号、Reddit 帖子、即刻动态、YouTube 视频——8 个扩展技能，覆盖主流平台。

但 1.x 的架构有个致命问题：**每个技能都是一个独立的 OpenClaw 子插件**。

- 安装一个技能 → 下载 ZIP → 解压 → `npm install` → 手动往 `~/.openclaw/openclaw.json` 里加插件路径 → 重启 OpenClaw
- 启停一个技能 → 找到对应的 `plugins.entries` 条目 → 改 `enabled` → 重启
- 每个技能目录下都有自己的 `openclaw-plugin/` 子目录（`index.mjs` + `openclaw.plugin.json` + `package.json`）
- 装了 8 个技能，配置文件里就有 9 个插件条目（1 个主插件 + 8 个子插件）

读者问得最多的问题不是"怎么配"，而是："XX 网站能不能用？"——答案永远是"等有人写那个技能"。能力天花板是"已开发技能的数量"。

2.0 的核心目标：打破这个天花板。

---

## 2. 2.0 架构变更（完整对比）

### 2.1 版本时间线

| 版本 | 日期 | 关键变化 |
|------|------|----------|
| 1.3.x | 2026-01 | 初始公开版本，核心浏览器自动化 |
| 1.4.0 | 2026-02-24 | 自动服务发现、统一 URL、自适应认证 |
| 1.5.0 | 2026-04 | 统一运行时目录 `~/.js-eyes`、CLI 开发模式 |
| 1.5.1 | 2026-04 | Legacy 数据自动迁移、版本同步 |
| **2.0.0** | **2026-04-14** | **单一主插件 + 技能自动发现（本次升级核心）** |
| 2.0.1 | 2026-04-14 | skill contract 加载路径修复、文档刷新 |

### 2.2 架构翻转：1.x vs 2.0

| 维度 | 1.x | 2.0 |
|------|-----|-----|
| **插件数量** | 1 个主插件 + N 个子插件（每个技能一个） | 只有 1 个主插件 |
| **技能注册** | 手动加 `plugins.load.paths` | 主插件启动时扫描 `skills/` 目录自动注册 |
| **技能启停** | 改 `plugins.entries[skillId].enabled` | 改 JS Eyes 配置的 `skillsEnabled[skillId]` |
| **技能目录结构** | 每个技能有 `openclaw-plugin/` 子目录 | 不再有 `openclaw-plugin/`，只有 `skill.contract.js` |
| **安装流程** | 下载→解压→装依赖→改配置→重启（5 步） | 下载→解压→装依赖→写启用状态→重启（自动完成） |
| **配置写入** | 直接改 `~/.openclaw/openclaw.json` | 写入 JS Eyes 本地配置，主插件自动读取 |
| **代码行数变化** | — | **-309 行**（75 文件变更：+475 / -784） |

### 2.3 关键代码变更

**删除的内容（每个技能下）：**
```
skills/js-x-ops-skill/openclaw-plugin/
├── index.mjs          ← 删除（23 行）
├── openclaw.plugin.json  ← 删除（44 行）
└── package.json       ← 删除（17 行）
```

8 个技能 × 3 个文件 = **24 个文件被删除**。

**新增的核心机制（主插件中）：**
```javascript
// openclaw-plugin/index.mjs（2.0）
const {
  discoverLocalSkills,
  loadSkillContract,
  registerOpenClawTools,
  isSkillEnabled,
  // ...
} = require("../packages/protocol/skills");

function registerLocalSkills(api, skillsDir, pluginConfig) {
  const localSkills = discoverLocalSkills(skillsDir);  // 扫描目录
  for (const skill of localSkills) {
    if (!isSkillEnabled(hostConfig, skill.id)) continue; // 检查启用状态
    const contract = loadSkillContract(skill.skillDir);  // 加载合约
    const adapter = contract.createOpenClawAdapter(...); // 创建适配器
    registerOpenClawTools(api, adapter, ...);           // 注册工具
  }
}
```

**skill.contract.js（技能合约）—— 每个技能的唯一接口：**
```javascript
// skills/js-x-ops-skill/skill.contract.js
module.exports = {
  id: 'js-x-ops-skill',
  name: 'JS X Ops Skill',
  version: '2.0.0',
  description: 'X.com content operations...',
  // 核心：工厂函数，返回 OpenClaw 工具列表
  createOpenClawAdapter(pluginConfig, logger) {
    return {
      id: 'js-x-ops-skill',
      tools: [
        { name: 'x_search_tweets', label: '...', description: '...', parameters: {...}, execute: fn },
        { name: 'x_get_profile', ... },
        { name: 'x_get_post', ... },
        { name: 'x_get_home_feed', ... },
      ],
      cli: { entry: './index.js', commands: [...] },
      runtime: { requiresServer: true, requiresBrowserExtension: true, requiresLogin: true, platforms: ['x.com'] },
    };
  }
};
```

### 2.4 协议层重构（packages/protocol/skills.js）

2.0 新增了统一的技能协议层，提供：

| 函数 | 功能 |
|------|------|
| `discoverLocalSkills(skillsDir)` | 扫描目录，找到所有带 `skill.contract.js` 的子目录 |
| `loadSkillContract(skillDir)` | 加载技能合约（带 require.cache 清除，支持热更新） |
| `registerOpenClawTools(api, adapter)` | 将合约中的工具列表注册到 OpenClaw |
| `isSkillEnabled(config, skillId)` | 检查技能是否启用 |
| `getLegacyOpenClawSkillState()` | 从旧配置迁移启停状态（向后兼容） |
| `installSkillFromRegistry()` | 从注册表下载、安装、启用技能 |

### 2.5 Monorepo 结构（2.0 全新布局）

```
js-eyes/
├── apps/cli/                    # 公开的 npm CLI（js-eyes）
├── packages/
│   ├── protocol/               # 协议常量 + 技能兼容性矩阵
│   ├── runtime-paths/          # 运行时目录配置
│   ├── config/                 # CLI 配置加载与持久化
│   ├── client-sdk/             # Node.js 浏览器自动化 SDK
│   ├── server-core/            # HTTP + WebSocket 服务器
│   └── devtools/               # 构建/发布工具
├── openclaw-plugin/            # OpenClaw 插件（一等公民，根级目录）
├── skills/                     # 独立扩展技能
│   ├── js-x-ops-skill/        # X.com 操作（4 个工具）
│   ├── js-bilibili-ops-skill/ # Bilibili 视频
│   ├── js-youtube-ops-skill/  # YouTube 视频
│   ├── js-zhihu-ops-skill/    # 知乎内容
│   ├── js-xiaohongshu-ops-skill/ # 小红书
│   ├── js-wechat-ops-skill/   # 微信公众号
│   ├── js-reddit-ops-skill/   # Reddit
│   └── js-jike-ops-skill/     # 即刻
├── extensions/                 # 浏览器扩展
│   ├── chrome/
│   └── firefox/
└── docs/                       # 项目站点
```

**变化**：不再有根级 `server/`、`clients/`、`cli/` 兼容树。全部收敛到 `packages/` 下。

---

## 3. 关键决策

### 3.1 为什么不用子插件了？

**问题**：每个技能都需要独立的 `openclaw-plugin/` 包装，意味着：
- 维护成本高——每个技能 3 个包装文件，基本重复
- 用户负担重——安装 N 个技能要改 N 处配置
- 依赖关系混乱——技能依赖主插件，但主插件不知道技能的存在
- 启停状态分散——`plugins.entries` 里混着主插件和子插件

**类比**：给龙虾装了 10 只眼睛，每只要单独调焦距。

### 3.2 为什么选择 skill contract 模式？

每个技能只需要一个 `skill.contract.js` 文件，声明：
- `id / name / version / description`：元信息
- `createOpenClawAdapter()`：工厂函数，返回工具列表
- `cli`：CLI 命令定义
- `runtime`：运行时要求（是否需要服务器、浏览器扩展、登录态、目标平台）

主插件启动时：
1. 扫描 `skills/` 目录
2. 找到所有 `skill.contract.js`
3. 检查启用状态
4. 调用 `createOpenClawAdapter()` 获取工具
5. 注册到 OpenClaw

**核心变化**：从"你告诉龙虾有什么"到"龙虾自己发现有什么"。

### 3.3 向后兼容怎么处理？

2.0 保留了完整的迁移路径：
- `getLegacyOpenClawSkillState()` 自动读取旧配置中的 `plugins.entries` 启停状态
- 迁移到 JS Eyes 本地配置的 `skillsEnabled` 字段
- 迁移成功后写入日志：`Migrated N legacy OpenClaw skill state entries`

---

## 4. 技能生态现状

### 4.1 已发布的 8 个扩展技能

| 技能 ID | 平台 | 工具数 | 需要登录 | 说明 |
|---------|------|--------|----------|------|
| js-x-ops-skill | x.com | 4 | ✅ | 搜索/主页/帖子详情/发帖 |
| js-bilibili-ops-skill | bilibili.com | 2 | ✅ | 视频元数据/字幕 |
| js-youtube-ops-skill | youtube.com | 2 | ✅ | 视频元数据/字幕 |
| js-zhihu-ops-skill | zhihu.com | 2 | ❌ | 回答/专栏 |
| js-xiaohongshu-ops-skill | xiaohongshu.com | 1 | ❌ | 笔记详情 |
| js-wechat-ops-skill | mp.weixin.qq.com | 1 | ❌ | 公众号文章 |
| js-reddit-ops-skill | reddit.com | 1 | ❌ | 帖子详情+评论树 |
| js-jike-ops-skill | okjike.com | 1 | ❌ | 帖子详情+图片+评论 |

**基础工具（主插件内置）：9 个**
`js_eyes_get_tabs` / `js_eyes_list_clients` / `js_eyes_open_url` / `js_eyes_close_tab` / `js_eyes_get_html` / `js_eyes_execute_script` / `js_eyes_get_cookies` / `js_eyes_discover_skills` / `js_eyes_install_skill`

**合计：17 个 AI 工具**

### 4.2 技能注册表

在线注册表：https://js-eyes.com/skills.json
- `parentSkill.version: "2.0.1"`
- 技能版本全部对齐到 2.0.0
- 安装命令统一：`curl -fsSL https://js-eyes.com/install.sh | bash -s -- <skillId>`

---

## 5. 对 KL28 文章的启示

### 5.1 叙事角度

不要写成"插件配置变简单了"——太工程化。

核心叙事：**龙虾的能力边界彻底打开了。**

- 1.x 时代：龙虾能用哪些网站，取决于"已开发技能的数量"
- 2.0 时代：主插件自动发现所有已安装技能，安装一个技能 = 自动可用
- 未来方向：任何人基于 `@js-eyes/client-sdk` 写一个 `skill.contract.js`，就能让龙虾学会操作任何网站

### 5.2 关键对比

| 1.x | 2.0 |
|-----|-----|
| 装什么会什么 | 看到什么学什么（前提是有人写了合约） |
| 能力天花板 = 已开发技能数 | 能力天花板 = 有人写过合约的网站数 |
| 每装一个技能改多处配置 | 装完重启即可 |
| 子插件地狱 | 单一主插件 + 自动发现 |

### 5.3 核心金句候选

- "2.0 之后，龙虾不需要你告诉它有什么新能力。它自己会去看。"
- "之前是装什么会什么，现在是看到什么学什么。"
- "给龙虾装眼睛（KL03）→ 让眼睛学会自己发现新能力（KL28）"

---

## 6. 后续演化

- [ ] 将本地 develop 分支合并 main 上的 2.0.1 变更
- [ ] 本地 OpenClaw 配置从子插件模式迁移到单一主插件模式
- [ ] KL28 文章创作
- [ ] 考虑开发通用技能合约模板，降低新技能开发门槛
