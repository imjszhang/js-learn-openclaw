# KL17 选题：从消费者到生产者——中国镜像与技能开发

> 日期：2026-04-02
> 项目：养虾日记系列（公众号）
> 类型：选题策划
> 来源：JS 的选题需求

---

## 1. 背景与动机

**新闻引入点**：
- OpenClaw 官方中国镜像上线（mirror-cn.clawhub.com）
- 基础设施：字节跳动火山引擎赞助
- 数据：836赞/115转推/71回复（截至 2026-04-02 02:22）
- 意义：不仅是访问速度，更是「生态主权」转移的信号

**选题角度**：
不是简单报道镜像上线，而是借这个节点教读者：
1. 如何在中国镜像安装技能（消费者）
2. 如何在本地切换镜像源（进阶用户）
3. 如何制作第一个技能（生产者）
4. 如何上传技能到官方市场（发布者）
5. 展示已上架的三个技能作为案例

---

## 2. 核心素材

### 已上架技能清单

| 技能 | 功能 | 上架时间 | ClawHub |
|------|------|----------|---------|
| js-eyes | 浏览器自动化 | 2026-03 早期 | clawhub.ai/imjszhang/js-eyes |
| js-comfyui-client | ComfyUI 图片/视频生成 | 2026-03-25 | clawhub.ai/imjszhang/js-comfyui-client |
| js-x-monitor | X.com 监控 | 2026-03-31 | clawhub.ai/imjszhang/js-x-monitor |

### 技能开发故事

**js-eyes（第一个技能）**
- 起源：OpenClaw 想读公众号文章，登录态搞不定
- 解决：浏览器扩展做桥梁，让龙虾控制浏览器
- 转折：发现不仅是「登录工具」，更是「给龙虾装上眼睛」
- 成果：解决公众号/小红书/知乎/即刻登录墙

**js-comfyui-client（最实用）**
- 起源：ComfyUI 工作流太复杂，参数靠记忆
- 解决：封装常用工作流为 CLI，一条命令生成
- 特色：Z-Image Turbo（9步快生）、Wan 2.2 视频
- 数据：KL11/12 配图全部用它生成

**js-x-monitor（最新）**
- 起源：想追踪 Karpathy/a16z/OpenClaw 动态，手动刷太累
- 解决：定时监控+智能摘要+飞书通知
- 实战：2026-04-02 02:19 成功抓到 a16z 关于「AI改变软件开发」推文
- 监控账号：@karpathy @openclaw @a16z @moltbook @steipete

---

## 3. 文章结构

### 引子：为什么中国镜像不只是「快」
- 现象：镜像上线，速度提升
- 转折：真正重要的是「生态主权」转移
- 论点：从「等官方」到「自己动手」，OpenClaw 的第二次跃迁

### 第一部分：在中国镜像安装技能（消费者）
- 步骤：mirror-cn.clawhub.com → Skills → 一键安装
- 对比：与主站同步，访问更快
- 实例：安装 js-eyes / js-x-monitor / js-comfyui-client

### 第二部分：本地切换镜像源（进阶用户）
- **核心命令**（npm）：`npx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com`
- **pnpm**：`pnpm dlx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com`
- **bun**：`bunx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com`
- 实例：`npx clawhub@latest install js-eyes --registry=https://cn.clawhub-mirror.com`

### 第三部分：制作第一个技能（生产者）
- 前置：为什么要技能化？（可复用/可分享/可维护）
- 工具：Skill Creator（内置）
- 结构：SKILL.md + tools/ + assets/

### 第四部分：上传到官方市场（发布者）
- 流程：开发→测试→打包→提交→审核→上线
- 标准：官方质量要求（待查证）

### 第五部分：我的三个技能与心得（实战展示）
- js-eyes / js-comfyui-client / js-x-monitor
- 开发心得：从「给自己用」到「给别人用」
- 设计哲学：一个技能 = 一个具体问题

### 结尾：邀请读者成为共建者
- 中国镜像 = 中国开发者更容易参与
- 呼应开头：从「用龙虾」到「养龙虾」到「繁殖龙虾」

---

## 4. 待确认事项

| 事项 | 状态 | 备注 |
|------|------|------|
| 镜像切换配置字段 | ❓待确认 | openclaw.json 哪个字段？ |
| Skill 提交流程 | ✅已确认 | ClawHub 网页表单（clawhub.ai/submit），需通过基础安全扫描 |
| 开篇角度 | ❓待选择 | A新闻解读 / B个人故事 |

---

## 5. 关联信息

- 视角：P25-yangxia-series
- KL展开文件：KL17-become-skill-creator.md
- 产出模板：yangxia-series
- 预计产出：17.md

