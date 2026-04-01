# KL17 - 从消费者到生产者：在中国镜像上安装、切换、制作和发布你的第一个技能

> 核心论点：中国镜像上线不只是「访问更快」，更是一个信号——OpenClaw 生态正在从「官方独占」走向「社区共建」。这篇是教你从「用技能的人」变成「做技能的人」的完整路径。

---

## 支撑材料

### 新闻引入点
- 事件：OpenClaw 官方中国镜像上线（mirror-cn.clawhub.com）
- 基础设施：字节跳动火山引擎赞助
- 数据：832赞/12.4万浏览
- 意义：不仅是访问速度，更是生态开放的信号

### 核心素材
1. 中国镜像与主站的关系（同步机制、使用方式）
2. 官方 Skills 市场：https://clawhub.ai/skills
3. 中国镜像 Skills 市场：https://js-clawhub.com/skills
4. 已上传的插件/技能清单（待用户补充）
5. Skill Creator 技能使用经验（2026-03-13 安装的 anthropic/skill-creator）

---

## 逻辑结构

### 引子：为什么中国镜像不只是「快」
- 现象：镜像上线，访问速度提升
- 转折：真正重要的不是速度，是「生态主权」的转移
- 论点：从「等官方」到「自己动手」，这是 OpenClaw 的第二次跃迁

### 第一部分：如何在中国镜像安装技能（消费者视角）
- 步骤：打开 mirror-cn.clawhub.com → 找到 Skills → 一键安装
- 对比：与主站的同步关系（内容一致，访问更快）
- 实例：安装 js-eyes / knowledge-prism / comfyui-client 等常用技能
- 技巧：如何查看技能详情、版本、使用文档

### 第二部分：如何在本地切换镜像源（进阶用户）
- 场景：已经配置了主站，想切到中国镜像
- **核心命令**（npm方式）：
  ```bash
  npx clawhub@latest install <skill-name> --registry=https://cn.clawhub-mirror.com
  ```
- 其他方式：
  - pnpm: `pnpm dlx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com`
  - bun: `bunx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com`
- 实例：安装 js-eyes
  ```bash
  npx clawhub@latest install js-eyes --registry=https://cn.clawhub-mirror.com
  ```
- 同步机制：中国镜像与主站内容实时同步，仅加速访问

### 第三部分：如何制作你的第一个技能（生产者视角）
- 前置：为什么要把你的配置/脚本变成技能？
  - 可复用：一次制作，多处安装
  - 可分享：社区共建，互惠互利
  - 可维护：版本管理，迭代更新
- 工具：Skill Creator 技能（已内置）
  - 读取 SKILL.md 了解结构
  - 创建技能骨架
  - 填充 Prompt 和工具定义
- 最小可运行技能的结构：
  - SKILL.md（核心定义）
  - tools/（工具实现）
  - assets/（资源文件）

### 第四部分：如何上传技能到官方市场（发布者视角）
- 流程：开发 → 测试 → 打包 → 提交 → 审核 → 上线
- **提交方式**：ClawHub 网页表单提交（clawhub.ai/submit）
- **审核标准**：
  - 基础安全扫描通过
  - SKILL.md 文档完整
  - 工具功能清晰、无副作用
  - 开源协议明确（推荐 MIT）
- **我的上架经验**：
  - js-eyes：首个技能，审核关注浏览器权限说明
  - js-comfyui-client：需说明本地 ComfyUI 依赖
  - js-x-monitor：需说明 X.com 数据获取合规性
- 收益：不只是分享，更是个人品牌的建立

### 第五部分：我的三个技能与开发心得（实战展示）

#### 已上架技能清单

| 技能 | 功能 | 使用场景 | ClawHub链接 |
|------|------|----------|-------------|
| **JS ComfyUI Client** | ComfyUI 图片/视频生成 | 本地 AI 绘画、图生视频、批量生成 | clawhub.ai/imjszhang/js-comfyui-client |
| **JS X Monitor** | X.com 账号动态监控 | 自动追踪大 V 推文、热点捕捉、信息收集 | clawhub.ai/imjszhang/js-x-monitor |
| **JS Eyes** | 浏览器自动化控制 | 解决登录态难题、网页数据采集、自动化操作 | clawhub.ai/imjszhang/js-eyes |

#### 每个技能的故事

**JS Eyes（第一个技能）**
- 痛点：OpenClaw 想读取我收藏的公众号文章，但登录态搞不定
- 解决：用浏览器扩展做桥梁，让龙虾能控制我的浏览器
- 转折：发现这不仅是「登录工具」，更是「给龙虾装上眼睛」
- 成果：解决了公众号/小红书/知乎/即刻等所有登录墙问题

**JS ComfyUI Client（最实用的技能）**
- 痛点：ComfyUI 工作流太复杂，参数调整靠记忆
- 解决：把常用工作流封装成 CLI，一条命令生成图片/视频
- 特色：支持 Z-Image Turbo（9步快生）、Wan 2.2 视频生成
- 数据：KL11/12 的配图全部用它生成，Neo-Brutalist 风格可控

**JS X Monitor（最新的技能）**
- 痛点：想追踪 Andrej Karpathy/a16z/OpenClaw 官方动态，手动刷太浪费时间
- 解决：定时监控 + 智能摘要 + 飞书通知
- 实战：刚才成功抓到 a16z 关于「AI改变软件开发」的推文
- 数据：已监控 @karpathy @openclaw @a16z @moltbook 等账号

#### 开发心得

**从「给自己用」到「给别人用」的转变**
- 第一个版本：硬编码路径、写死配置、只在我电脑上能跑
- 技能化改造：提取配置、补充文档、设计 CLI 接口、写 SKILL.md
- 收获：强迫自己把「潜规则」变成「显式规则」，代码质量反而提升

**一个技能 = 一个具体问题**
- 不搞大而全，只做一件事但做到极致
- JS Eyes = 浏览器控制，不涉及数据处理
- JS X Monitor = 监控+通知，不涉及内容分析
- JS ComfyUI = 图片生成，不涉及提示词工程

**上架不是终点，是起点**
- 用户反馈会暴露你没想到的场景
- 版本迭代让技能越来越 robust
- 社区贡献让你获得意外收获（比如被官方转发）

### 结尾：邀请读者成为共建者
- 中国镜像 = 中国开发者更容易参与
- 下一个技能，可能来自你
- 呼应开头：从「用龙虾」到「养龙虾」到「繁殖龙虾」

---

## 引用

### Synthesis（顶层观点）
- S27: OpenClaw 作为个人 AI 操作系统的定位
- S45: 技能（Skill）是能力扩展的核心机制
- S62: ACP 与外部 Agent 的协作模式
- （待补充 Sxx: 生态共建与社区化）

### Groups（中层分组）
- G34: JS-Eyes 浏览器自动化技能
- G64: 登录态与持久化技术
- （待补充：技能开发与发布的 Groups）

### Atoms（底层信息单元）
- （待补充：中国镜像上线的 atoms）
- （待补充：Skill Creator 使用经验的 atoms）

---

## 写作要点

1. **开篇钩子**：用「中国镜像上线」这个热点切入，但不是简单报道，而是解读信号
2. **递进结构**：消费者 → 进阶用户 → 生产者 → 发布者，层层推进
3. **实操导向**：每个部分都要有「可执行步骤」，不只是概念
4. **个人案例**：最后一部分必须用用户自己的技能做展示，增强真实感和说服力
5. **结尾升华**：从个人使用到社区共建，呼应养虾日记系列的成长主线

## 状态

- [ ] 素材收集中（等待用户补充已上传技能清单）
- [ ] 镜像切换技术细节确认
- [ ] Skill 提交审核流程查证
- [ ] 待写作

