# G86: js-knowledge-prism 发布到 ClawHub 必须采用"Plugin 形态 + 绝对路径 + 语义化版本"策略以规避技能能力限制与 Windows 路径解析陷阱

> 将复杂工具发布到 ClawHub 时，必须严格区分 Skill 与 Plugin 的能力边界，并针对 Windows 环境的路径解析缺陷采用绝对路径工作流。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| PK-01 | publish-js-knowledge-prism-to-clawhub | ClawHub 是 OpenClaw 的公共注册表，托管 Skills 和 Plugins，支持语义搜索和 semver 版本管理 |
| PK-02 | publish-js-knowledge-prism-to-clawhub | Skill 是含 SKILL.md 的纯指令目录，用于教 Agent 使用工具；Plugin 是含 openclaw.plugin.json 的可执行包 |
| PK-03 | publish-js-knowledge-prism-to-clawhub | 发布 ClawHub 前需安装 CLI (`npm i -g clawhub`) 并登录，且 GitHub 账号需注册满约一周以防滥用 |
| PK-04 | publish-js-knowledge-prism-to-clawhub | js-knowledge-prism 必须发布为 Plugin 形态，因为其核心能力（工具注册、CLI、HTTP 路由、Cron）是 Skill 无法实现的 |
| PK-05 | publish-js-knowledge-prism-to-clawhub | 项目根目录的 SKILL.md 应定位为插件随附文档而非独立 Skill，随 Plugin 一起分发以指导 Agent 使用 |
| PK-06 | publish-js-knowledge-prism-to-clawhub | 发布渠道首选 ClawHub 而非 npm，因为 ClawHub 是 OpenClaw 原生渠道且安装优先级更高 |
| PK-07 | publish-js-knowledge-prism-to-clawhub | 使用 Token 登录 ClawHub CLI 的命令为 `clawhub login --token "<token>" --no-browser` |
| PK-08 | publish-js-knowledge-prism-to-clawhub | 在 Windows 下执行 `clawhub publish` 时，使用相对路径 `.` 会报错提示缺少 SKILL.md，需改用完整绝对路径才能成功 |
| PK-09 | publish-js-knowledge-prism-to-clawhub | 发布命令需指定 slug、name、version 和 tags，例如 `clawhub publish <path> --slug <name> --version <ver>` |
| PK-10 | publish-js-knowledge-prism-to-clawhub | ClawHub 安全扫描标记"suspicious patterns"是预期行为，若 SKILL.md 中包含读取环境变量或配置文件的指令 |
| PK-11 | publish-js-knowledge-prism-to-clawhub | 新发布条目不会立即出现在语义搜索结果中，因为向量搜索索引存在延迟 |
| PK-12 | publish-js-knowledge-prism-to-clawhub | 用户可通过 `clawhub install <slug>` 或 `openclaw plugins install clawhub:<slug>` 安装已发布的插件 |
| PK-13 | publish-js-knowledge-prism-to-clawhub | 后续版本更新可使用 `clawhub publish --version <新版本>` 或 `clawhub sync` 命令进行批量同步 |

## 组内逻辑顺序

按照“平台认知 -> 形态决策 -> 前置准备 -> 执行细节 (含故障排查) -> 验证与运维”的逻辑顺序排列。首先明确 ClawHub 定位与 Skill/Plugin 区别 (PK-01, PK-02)，据此确定 js-knowledge-prism 必须为 Plugin (PK-04, PK-05, PK-06)；接着列出账号与 CLI 准备 (PK-03, PK-07)；随后详述发布命令格式及 Windows 特有路径陷阱 (PK-08, PK-09)，并解释安全扫描误报 (PK-10)；最后说明索引延迟 (PK-11)、安装方式 (PK-12) 及版本更新策略 (PK-13)。
