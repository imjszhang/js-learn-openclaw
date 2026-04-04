# KL17 - 讲讲 OpenClaw 的 skills 和 plugin 系统：小龙虾的执行能力来源

> 核心论点：中国镜像上线不只是「下载更快」，它背后是一套让 OpenClaw 从「聊天工具」变成「能干活的 Agent」的扩展系统。Skill 和 Plugin 就是小龙虾的「手」和「大脑」。

---

## 支撑材料

### 新闻引入点
- 事件：OpenClaw 官方中国镜像上线（2026-04-01）
- 地址：https://mirror-cn.clawhub.com
- 基础设施：字节跳动火山引擎赞助
- 数据：832赞 / 114转发 / 12.4万浏览
- 意义：降低国内用户门槛，更重要的是让「技能扩展」真正触手可及

### 核心概念区分
| 维度 | Skills | Plugins |
|------|--------|---------|
| 定位 | 功能扩展包 | 底层能力注入 |
| 比喻 | 给龙虾装「App」 | 给龙虾装「器官」 |
| 例子 | X搜索 / 知识收集 / 文生图 | JS-Eyes浏览器 / 微信接入 |
| 安装方式 | `clawhub install` | `npm install` + 配置 |
| 运行方式 | 作为工具被调用 | 注册为系统级能力 |
| 开发门槛 | 低（Prompt + 配置） | 中（需要写代码） |

### 已上架技能清单（用户）
| 技能 | 功能 | 场景 | 链接 |
|------|------|------|------|
| js-eyes | 浏览器自动化 | 登录态、网页抓取 | clawhub.ai/imjszhang/js-eyes |
| js-comfyui-client | ComfyUI 生图/视频 | 文章配图、海报 | clawhub.ai/imjszhang/js-comfyui-client |
| js-x-monitor | X.com 监控 | 大V动态追踪 | clawhub.ai/imjszhang/js-x-monitor |

### 核心素材
1. 官方 Skills 市场：https://clawhub.ai/skills
2. 中国镜像 Skills 市场：https://mirror-cn.clawhub.com/skills
3. 中国镜像切换命令：`--registry=https://mirror-cn.clawhub.com`
4. Skill 目录结构：SKILL.md / tools/ / assets/
5. Plugin 配置方式：openclaw.json 的 plugins.entries

---

## 逻辑结构

### 引子：中国镜像上线，不只是「快」
- 现象：ClawHub 中国镜像上线，访问速度提升
- 转折：真正重要的不是速度，是让「装技能」这件事变得简单
- 核心问题：为什么同样的 OpenClaw，有人只能聊天，有人却能自动监控 X、生成图片、管理知识库？
- 答案：区别就在 Skills 和 Plugins——OpenClaw 的扩展系统给了它「做事的能力」

### 第一部分：Skills vs Plugins——两套扩展系统

#### 一句话区分
- **Skill** = 给龙虾装「App」（具体任务能力）
- **Plugin** = 给龙虾装「器官」（基础能力扩展）

#### Skills：功能级别的扩展
- 官方内置：文件读写、网络请求、代码执行（出厂配置）
- 社区技能：从 ClawHub 安装，即装即用
- 自定义技能：你自己的业务逻辑封装

#### Plugins：系统级别的扩展
- 通过 npm 安装，在 openclaw.json 中配置
- 注入新的工具到 Agent 的工具箱
- 例子：js-eyes 注入浏览器控制工具、openclaw-weixin 注入微信消息能力

#### 为什么需要两套系统？
| 场景 | 选择 |
|------|------|
| 想给 Agent 加个新功能 | Skill |
| 想扩展 OpenClaw 底层能力 | Plugin |
| 封装一个可复用的工作流 | Skill |
| 接入一个新的外部系统 | Plugin |

### 第二部分：执行能力从哪来？（四层架构）

#### Layer 1: 官方内置能力（Core）
- 文件系统：read / write / edit / exec
- 网络能力：web_search / web_fetch
- 代码执行：各种语言的运行环境
- 这些是「出厂配置」，不需要安装

#### Layer 2: 社区技能市场（ClawHub）
- 安装后，龙虾「学会」了新技能
- 例子：
  - x-search-x：X.com 搜索能力
  - knowledge-collector：文章抓取能力
  - knowledge-prism：知识处理能力
  - comfyui-client：AI 图像生成能力

#### Layer 3: 第三方插件（Plugins）
- 安装后，龙虾「长出」了新器官
- 例子：
  - js-eyes：浏览器控制能力
  - openclaw-weixin：微信消息能力

#### Layer 4: 自定义技能（Private）
- 你自己的业务逻辑
- 安装后，龙虾有了「专属武器」

### 第三部分：实战——在中国镜像安装技能

#### Step 1: 切换镜像源
```bash
openclaw config set registry https://mirror-cn.clawhub.com
```

#### Step 2: 搜索技能
```bash
openclaw skill search x
```

#### Step 3: 安装技能
```bash
openclaw skill install x-search-x
```

#### Step 4: 验证安装
```bash
openclaw tools list | grep x
```

#### 临时使用中国镜像（不切换默认源）
```bash
openclaw skill install x-search-x --registry https://mirror-cn.clawhub.com
```

### 第四部分：我的技能组合与使用场景

| 技能/插件 | 类型 | 能力 | 我的使用场景 |
|-----------|------|------|-------------|
| x-search-x | Skill | X.com 搜索 | 监控 a16z/openclaw 动态 |
| knowledge-collector | Skill | 文章抓取 | 公众号/知乎/即刻文章入库 |
| knowledge-prism | Skill | 知识处理 | Journal→Atoms→产出 |
| comfyui-client | Skill | AI 生图 | 文章配图、海报生成 |
| js-eyes | Plugin | 浏览器控制 | 网页自动化、登录态处理 |
| openclaw-weixin | Plugin | 微信接入 | 消息收发、群聊管理 |

#### JS Eyes 的故事
- 痛点：OpenClaw 想读取我收藏的公众号文章，但登录态搞不定
- 解决：用浏览器扩展做桥梁，让龙虾能控制我的浏览器
- 发现：这不仅是「登录工具」，更是「给龙虾装上眼睛」
- 成果：解决了公众号/小红书/知乎/即刻等所有登录墙问题

#### JS ComfyUI Client 的故事
- 痛点：ComfyUI 工作流太复杂，参数调整靠记忆
- 解决：把常用工作流封装成 CLI，一条命令生成图片/视频
- 特色：支持 Z-Image Turbo（9步快生）、Wan 2.2 视频生成
- 数据：养虾日记的配图全部用它生成，Neo-Brutalist 风格可控

#### JS X Monitor 的故事
- 痛点：想追踪 Karpathy/a16z/OpenClaw 动态，手动刷太浪费时间
- 解决：定时监控 + 智能摘要 + 飞书通知
- 实战：成功抓取 a16z 关于「AI改变软件开发」的推文
- 数据：已监控 @karpathy @openclaw @a16z @moltbook 等账号

### 第五部分：Skill 系统的技术设计（浅析）

#### 1. 工具注册机制
- Skill 通过 SKILL.md 声明提供的工具
- 安装后注入到 Agent 的可用工具列表
- Agent 根据任务选择合适的工具

#### 2. 版本与依赖管理
- 语义化版本控制（semver）
- 自动处理依赖冲突
- 支持版本回滚

#### 3. 安全隔离
- Skill 运行在沙箱环境
- 敏感操作需用户授权
- 文件访问有权限边界

#### 4. 中国镜像的价值
- 加速下载（ obvious ）
- 降低国内开发者参与门槛
- 本地化生态建设起点
- 内容实时同步主站

### 第六部分：如何制作和发布技能

#### 为什么要把配置/脚本变成技能？
- **可复用**：一次制作，多处安装
- **可分享**：社区共建，互惠互利
- **可维护**：版本管理，迭代更新

#### 最小可运行 Skill 的结构
```
skill-name/
├── SKILL.md          # 核心定义（必需）
├── tools/
│   └── tool-name.js  # 工具实现
├── assets/           # 资源文件
└── package.json      # 依赖声明
```

#### SKILL.md 的核心内容
- 技能描述和适用场景
- 提供的工具列表
- 配置参数说明
- 使用示例

#### 发布流程
1. 开发 → 2. 测试 → 3. 打包 → 4. 提交 → 5. 审核 → 6. 上线

**提交方式**：ClawHub 网页表单提交（clawhub.ai/submit）

**审核标准**：
- 基础安全扫描通过
- SKILL.md 文档完整
- 工具功能清晰、无副作用
- 开源协议明确（推荐 MIT）

**我的上架经验**：
- js-eyes：首个技能，审核关注浏览器权限说明
- js-comfyui-client：需说明本地 ComfyUI 依赖
- js-x-monitor：需说明 X.com 数据获取合规性

### 结尾：从「会用」到「会装」再到「会做」

中国镜像上线，让 Skills 和 Plugins 的安装变得触手可及。

但这只是起点。

- **第一阶段**：学会安装别人的技能（会用）
- **第二阶段**：学会切换镜像、配置插件（会装）
- **第三阶段**：做出自己的技能、反哺社区（会做）

这篇文章讲的是前两个阶段——让你理解 OpenClaw 的执行能力从哪来，以及如何获取它。

下一步，可能是第三阶段——把你反复使用的 Prompt、脚本、工作流，打包成一个 Skill，上传到 ClawHub。

到那时候，你就不再只是「养龙虾的人」，而是「繁殖龙虾的人」。

---

## 引用

### Synthesis（顶层观点）
- S27: OpenClaw 作为个人 AI 操作系统的定位
- S45: 技能（Skill）是能力扩展的核心机制
- S62: ACP 与外部 Agent 的协作模式

### Groups（中层分组）
- G34: JS-Eyes 浏览器自动化技能
- G64: 登录态与持久化技术
- G73: OpenClaw 技能/插件扩展机制

### Atoms（底层信息单元）
- Axx: 中国镜像上线（2026-04-01）
- Axx: Skills vs Plugins 核心区别
- Axx: 镜像切换命令与配置
- Axx: Skill 目录结构与 SKILL.md 规范
- Axx: ClawHub 提交审核流程

---

## 写作要点

1. **开篇钩子**：用「中国镜像上线」热点切入，但迅速转向「执行能力」这个核心主题
2. **概念清晰**：Skills vs Plugins 的对比表格要前置，让读者先建立认知框架
3. **分层递进**：从概念区分 → 架构分层 → 实战操作 → 个人案例 → 技术浅析
4. **案例真实**：三个技能（js-eyes/comfyui/x-monitor）必须有具体使用场景和数据
5. **行动导向**：每个部分都要给出可执行的命令或步骤
6. **结尾呼应**：从「会用」到「会装」再到「会做」，呼应技能学习的三个阶段

## 状态

- [x] KL 框架完成（2026-04-02）
- [ ] 创建飞书文档
- [ ] 产出正文
