# KL14-x-monitoring.md

## 核心论点

通过 JS-Eyes 复用用户浏览器登录态，配合 Cron 定时任务，实现 X.com 账号的自动监控与情报推送——解决信息过载、时区错位、手动刷推低效的问题。

## 支撑论据

### 论据 1：为什么"人工+RSS+第三方工具"都不行？

| 方案 | 问题 |
|------|------|
| 人工刷 | 累、漏、时区错位 |
| RSS | X.com 已关闭官方 RSS 接口 |
| 第三方工具 | 贵（$20+/月）、隐私风险、无法自定义 |

→ JS-Eyes + Cron = 自主可控的零成本方案

### 论据 2：技术架构三层分工

1. **感知层**：JS-Eyes 钻进 Chrome，复用登录态抓取时间线
2. **处理层**：Agent 解析 HTML 提取帖子内容，哈希去重
3. **通知层**：飞书消息实时推送，附带 AI 摘要

### 论据 3：核心难题——如何避免重复通知？

**方案 A**：帖子 ID 本地存储
- 优点：简单
- 缺点：编辑过的帖子会重复通知

**方案 B**：ID + 内容哈希双重校验
- 优点：编辑检测、更可靠
- 缺点：略复杂

**推荐**：B 方案

### 论据 4：7 天实战复盘（数据驱动）

**Day 0（技术方案迭代）**：
- 原计划：用 js_eyes_execute_script 直接解析 DOM
- 问题：@sama/@paulg/@karpathy 连续超时（120秒限制），5个任务全部卡住
- 诊断：发现 `x_get_profile` 在 Cron isolated session 中不可用
- 修复：OpenClaw 更新后扩展技能全局加载，js-search-x 现已可用
- 验证：22:27 测试成功，2.5秒获取 @karpathy 最新推文
- 最终名单：@karpathy @moltbook @a16z @openclaw @steipete（5个账号）
- 建立基线：抓取7天历史数据共96条推文

**Day 1-7（预测数据基于基线推算）**：
- 总通知：20-25条（去重后）
- 高价值推文：10-12条（信噪比50%）
- 精选案例：安全警告、产品洞察、技术深度、版本更新、工具评测
- Token消耗：~$0.25/周，~$1/月
- 关键场景：避免供应链攻击风险、第一时间升级新功能、调整运营策略

**Top 5 高价值推文案例**：
1. karpathy 的 litellm 供应链攻击警告（Mar 25）
2. karpathy 关于 DevOps 难度的洞察（Mar 27）
3. karpathy 关于 LLM 记忆问题的技术探讨（Mar 26）
4. openclaw 2026.3.28 版本更新（Mar 29）
5. steipete 关于 Codex CLI 的开发者反馈（Mar 29）

### 论据 7：开源分享——js-x-monitor-skill

**GitHub**: https://github.com/imjszhang/js-x-monitor-skill

把这套监控方案打包成了 OpenClaw Skill，开源给社区使用：

```bash
# 安装
openclaw skill install js-x-monitor

# 使用
openclaw x-monitor init
openclaw x-monitor add karpathy
openclaw x-monitor start
```

**功能特性**：
- 定时监控：每小时自动检查
- 智能去重：ID + 内容哈希双重校验
- 多平台通知：飞书/微信/Discord
- 零成本：~$1/月（5个账号）

**技术栈**：
- OpenClaw + JS-Eyes + js-search-x
- Node.js 脚本
- MIT License

### 论据 5：从"监控"到"情报"的进阶

- 自动摘要：100 字内提炼核心观点
- 智能标签：技术/产品/观点/闲聊 分类
- 周报生成：每周一早上自动推送"本周值得关注"

### 论据 6：成本与限制

| 项目 | 数值 |
|------|------|
| 监控 5 账号/周 | ~$0.25（实测推算）|
| 监控 5 账号/月 | ~$1.0 |
| 30 账号规模 | ~$6/月（仍远低于第三方工具 $20+/月）|
| Rate Limit 风险 | 低（js-search-x封装稳定）|
| 登录态过期 | 需定期重新授权 |
| 信噪比 | ~50%（10-12条高价值/20-25条通知）|

## 逻辑顺序

问题引入 → 方案对比 → 架构设计 → 核心难题 → 实战复盘 → 进阶玩法 → 成本提醒

## 引用 Atoms/Groups

- G34: JS-Eyes 通过 WebSocket 复用用户浏览器环境
- G64: JS Eyes 项目必须采用"浏览器主动连接 + 逆向架构"模式
- Journal/2026-03-31/kl14-x-monitoring.md: 7 天实战数据

## 状态

🆕 进行中（数据收集中）
