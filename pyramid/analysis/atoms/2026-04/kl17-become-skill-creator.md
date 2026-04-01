# KL17 选题：从消费者到生产者——中国镜像与技能开发

> 来源：[../../../../journal/2026-04-02/kl17-become-skill-creator.md](../../../../journal/2026-04-02/kl17-become-skill-creator.md)
> 缩写：SK

## Atoms

| 编号  | 类型 | 内容                                                                                               | 原文定位                    |
| ----- | ---- | -------------------------------------------------------------------------------------------------- | --------------------------- |
| SK-01 | 事实 | OpenClaw 官方中国镜像（mirror-cn.clawhub.com）已上线，由字节跳动火山引擎赞助                         | 背景与动机                  |
| SK-02 | 判断 | 中国镜像上线的意义不仅是访问速度提升，更是「生态主权」转移的信号                                   | 背景与动机                  |
| SK-03 | 步骤 | 在中国镜像安装技能的流程：访问 mirror-cn.clawhub.com → 进入 Skills 页面 → 一键安装                 | 文章结构 > 第一部分         |
| SK-04 | 步骤 | 使用 npm 切换镜像源安装技能的命令：`npx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com` | 文章结构 > 第二部分         |
| SK-05 | 步骤 | 使用 pnpm 切换镜像源安装技能的命令：`pnpm dlx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com` | 文章结构 > 第二部分         |
| SK-06 | 步骤 | 使用 bun 切换镜像源安装技能的命令：`bunx clawhub@latest install <skill> --registry=https://cn.clawhub-mirror.com` | 文章结构 > 第二部分         |
| SK-07 | 事实 | 技能开发的标准结构包含 SKILL.md 文件、tools 目录和 assets 目录                                     | 文章结构 > 第三部分         |
| SK-08 | 步骤 | 技能上传到官方市场的流程：开发→测试→打包→提交→审核→上线                                            | 文章结构 > 第四部分         |
| SK-09 | 经验 | 技能提交需通过 ClawHub 网页表单（clawhub.ai/submit）并经过基础安全扫描                               | 待确认事项                  |
| SK-10 | 事实 | js-eyes 技能通过浏览器扩展作为桥梁，解决了公众号、小红书、知乎、即刻等平台的登录墙问题               | 核心素材 > 技能开发故事     |
| SK-11 | 事实 | js-comfyui-client 技能封装了常用工作流为 CLI，支持 Z-Image Turbo（9 步快生）和 Wan 2.2 视频生成        | 核心素材 > 技能开发故事     |
| SK-12 | 事实 | js-x-monitor 技能通过定时监控、智能摘要和飞书通知，实现了对特定 Twitter 账号动态的自动追踪                   | 核心素材 > 技能开发故事     |
| SK-13 | 经验 | 技能设计的核心哲学是「一个技能 = 一个具体问题」，从给自己用到给别人用转变                             | 文章结构 > 第五部分         |
| SK-14 | 判断 | 文章选题角度应从单纯报道镜像上线转变为教读者完成从消费者到生产者的完整路径                           | 背景与动机                  |
| SK-15 | 事实 | 技能化带来的核心价值是可复用、可分享和可维护                                                       | 文章结构 > 第三部分         |
