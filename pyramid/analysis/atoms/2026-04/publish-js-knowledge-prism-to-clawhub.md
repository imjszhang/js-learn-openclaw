# 将 js-knowledge-prism 发布到 ClawHub

> 来源：[../../../../journal/2026-04-02/publish-js-knowledge-prism-to-clawhub.md](../../../../journal/2026-04-02/publish-js-knowledge-prism-to-clawhub.md)
> 缩写：PK

## Atoms

| 编号  | 类型 | 内容                                                                                               | 原文定位                    |
| ----- | ---- | -------------------------------------------------------------------------------------------------- | --------------------------- |
| PK-01 | 事实 | ClawHub 是 OpenClaw 的公共注册表，托管 Skills 和 Plugins，支持语义搜索和 semver 版本管理           | 分析过程：ClawHub 发布机制调研 |
| PK-02 | 事实 | Skill 是含 SKILL.md 的纯指令目录，用于教 Agent 使用工具；Plugin 是含 openclaw.plugin.json 的可执行包               | 分析过程：ClawHub 发布机制调研 |
| PK-03 | 事实 | 发布 ClawHub 前需安装 CLI (`npm i -g clawhub`) 并登录，且 GitHub 账号需注册满约一周以防滥用                      | 分析过程：ClawHub 发布机制调研 |
| PK-04 | 判断 | js-knowledge-prism 必须发布为 Plugin 形态，因为其核心能力（工具注册、CLI、HTTP 路由、Cron）是 Skill 无法实现的     | 方案设计：Skill 还是 Plugin？ |
| PK-05 | 判断 | 项目根目录的 SKILL.md 应定位为插件随附文档而非独立 Skill，随 Plugin 一起分发以指导 Agent 使用                      | 方案设计：Skill 还是 Plugin？ |
| PK-06 | 判断 | 发布渠道首选 ClawHub 而非 npm，因为 ClawHub 是 OpenClaw 原生渠道且安装优先级更高                                 | 方案设计：Skill 还是 Plugin？ |
| PK-07 | 步骤 | 使用 Token 登录 ClawHub CLI 的命令为 `clawhub login --token "<token>" --no-browser`                              | 实现要点：发布执行          |
| PK-08 | 经验 | 在 Windows 下执行 `clawhub publish` 时，使用相对路径 `.` 会报错提示缺少 SKILL.md，需改用完整绝对路径才能成功       | 实现要点：发布执行          |
| PK-09 | 步骤 | 发布命令需指定 slug、name、version 和 tags，例如 `clawhub publish <path> --slug <name> --version <ver>`          | 实现要点：发布执行          |
| PK-10 | 经验 | ClawHub 安全扫描标记"suspicious patterns"是预期行为，若 SKILL.md 中包含读取环境变量或配置文件的指令               | 验证与测试                  |
| PK-11 | 经验 | 新发布条目不会立即出现在语义搜索结果中，因为向量搜索索引存在延迟                                                 | 验证与测试                  |
| PK-12 | 步骤 | 用户可通过 `clawhub install <slug>` 或 `openclaw plugins install clawhub:<slug>` 安装已发布的插件                | 后续演化                    |
| PK-13 | 步骤 | 后续版本更新可使用 `clawhub publish --version <新版本>` 或 `clawhub sync` 命令进行批量同步                         | 后续演化                    |
