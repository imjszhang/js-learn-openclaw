# G87: 技能生态演进需依托「中国镜像基建 + 标准化开发范式 + 登录态桥接方案」实现从消费到生产的身份跃迁

> OpenClaw 生态通过上线中国镜像解决访问主权问题，并确立「一个技能=一个具体问题」的开发哲学，结合 JS-Eyes 等桥接方案突破平台登录墙，构建了完整的从技能消费者到生产者的转化路径。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| SK-01 | kl17-become-skill-creator | OpenClaw 官方中国镜像（mirror-cn.clawhub.com）已上线，由字节跳动火山引擎赞助 |
| SK-02 | kl17-become-skill-creator | 中国镜像上线的意义不仅是访问速度提升，更是「生态主权」转移的信号 |
| SK-03 | kl17-become-skill-creator | 在中国镜像安装技能的流程：访问 mirror-cn.clawhub.com → 进入 Skills 页面 → 一键安装 |
| SK-04 | kl17-become-skill-creator | 使用 npm 切换镜像源安装技能的命令：`npx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com` |
| SK-05 | kl17-become-skill-creator | 使用 pnpm 切换镜像源安装技能的命令：`pnpm dlx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com` |
| SK-06 | kl17-become-skill-creator | 使用 bun 切换镜像源安装技能的命令：`bunx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com` |
| SK-07 | kl17-become-skill-creator | 技能开发的标准结构包含 SKILL.md 文件、tools 目录和 assets 目录 |
| SK-08 | kl17-become-skill-creator | 技能上传到官方市场的流程：开发→测试→打包→提交→审核→上线 |
| SK-09 | kl17-become-skill-creator | 技能提交需通过 ClawHub 网页表单（clawhub.ai/submit）并经过基础安全扫描 |
| SK-10 | kl17-become-skill-creator | js-eyes 技能通过浏览器扩展作为桥梁，解决了公众号、小红书、知乎、即刻等平台的登录墙问题 |
| SK-11 | kl17-become-skill-creator | js-comfyui-client 技能封装了常用工作流为 CLI，支持 Z-Image Turbo（9 步快生）和 Wan 2.2 视频生成 |
| SK-12 | kl17-become-skill-creator | js-x-monitor 技能通过定时监控、智能摘要和飞书通知，实现了对特定 Twitter 账号动态的自动追踪 |
| SK-13 | kl17-become-skill-creator | 技能设计的核心哲学是「一个技能 = 一个具体问题」，从给自己用到给别人用转变 |
| SK-14 | kl17-become-skill-creator | 文章选题角度应从单纯报道镜像上线转变为教读者完成从消费者到生产者的完整路径 |
| SK-15 | kl17-become-skill-creator | 技能化带来的核心价值是可复用、可分享和可维护 |

## 组内逻辑顺序

按「基础设施升级（镜像）→ 开发规范（结构与流程）→ 典型实践（登录态桥接与具体案例）→ 核心哲学与价值」的逻辑顺序排列，展示从环境准备到思想升华的完整路径。
