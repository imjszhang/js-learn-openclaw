# G83: X.com 账号自动监控需构建"JS-Eyes 登录态复用 + 内容哈希去重 + 成本可控 Cron"的闭环工作流

> 利用浏览器扩展复用用户登录态解决反爬难题，结合双重去重策略与低成本摘要模型，实现高价值信息源的自动化捕获与推送。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| XM-01 | kl14-x-monitoring        | 项目目标是利用 js-eyes 扩展实现对 @karpathy、@sama、@paulg 三个 X.com 账号的自动监控 |
| XM-02 | kl14-x-monitoring        | 该方案的核心价值在于解决信息过载与时区错位导致的漏看问题，并展示带登录态监控的独特优势 |
| XM-03 | kl14-x-monitoring        | 技术架构流程为：X.com 时间线 → JS-Eyes 扩展（复用登录态）→ WebSocket → OpenClaw Gateway → Agent/Cron → 飞书推送 |
| XM-04 | kl14-x-monitoring        | 使用 `js_eyes_open_url` 打开目标主页获取 tabId，再用 `js_eyes_execute_script` 提取帖子数组 |
| XM-05 | kl14-x-monitoring        | 去重策略采用内容哈希去重（方案 B），存储帖子 ID、内容哈希和时间戳，比对时先查 ID 再查哈希以防编辑重复 |
| XM-06 | kl14-x-monitoring        | 去重状态文件存储路径为 `~/.openclaw/workspace/x-monitor/state.json` |
| XM-07 | kl14-x-monitoring        | 数据存储结构包含 accounts 对象，每个账号记录 lastCheck 时间及包含 id/hash/timestamp/notified 的 posts 数组 |
| XM-08 | kl14-x-monitoring        | X.com 帖子容器的关键 DOM 选择器为 `article[data-testid="tweet"]` |
| XM-09 | kl14-x-monitoring        | 从 X.com 帖子链接 href 中提取正则 `/status/([0-9]+)` 作为帖子 ID |
| XM-10 | kl14-x-monitoring        | X.com 是 React 动态加载页面，解析脚本需等待页面稳定或模拟滚动才能获取完整内容 |
| XM-11 | kl14-x-monitoring        | Cron 调度配置为每小时执行一次（everyMs: 3600000），触发 agentTurn 消息检查指定账号动态 |
| XM-12 | kl14-x-monitoring        | 应对 X.com Rate Limit 的策略包括控制频率、复用登录态以及增加随机延迟 |
| XM-13 | kl14-x-monitoring        | 应对动态加载限制的策略是在 execute_script 中模拟滚动或仅监控首屏显示的 10-20 条内容 |
| XM-14 | kl14-x-monitoring        | 应对 DOM 频繁变动的策略是优先使用 data-testid 等稳定属性，并准备备用选择器 |
| XM-15 | kl14-x-monitoring        | 应对登录态过期的策略是检测登录状态，失效时发送通知提醒人工重新登录 |
| XM-16 | kl14-x-monitoring        | 预估月消耗 Token 约为 1.3M（抓取 1M+ 摘要 300K），按 Kimi K2 价格成本约 0.3-0.5 美元/月 |
| XM-17 | kl14-x-monitoring        | 数据收集计划从 03-31 搭建框架开始，至 04-05 统计 7 天数据并撰写成文 |
| XM-18 | kl14-x-monitoring        | 需记录的关键数据点包括通知总数、高价值帖子数、踩坑次数以及实际与预估的 Token 消耗对比 |

## 组内逻辑顺序

遵循“目标价值 → 架构流程 → 核心实现（抓取/去重/存储）→ 异常处理策略 → 成本与验证计划”的逻辑顺序排列。
