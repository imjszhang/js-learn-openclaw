# G100: JS-Eyes 2.0 通过“单一主插件 + 自动发现”架构重构，彻底打破技能扩展的预置天花板

> 从“用户手动配置冗余子插件”进化为“系统自动扫描技能合约”，不仅降低了维护成本，更将生态边界从“已开发数量”扩展至“任意可写合约的网站”。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| EA-01 | js-eyes-2-architecture-upgrade | 1.x 架构中每个技能都是独立的 OpenClaw 子插件，导致配置文件条目冗余且维护复杂 |
| EA-02 | js-eyes-2-architecture-upgrade | 1.x 架构的能力天花板受限于“已开发技能的数量”，因为用户无法自行扩展未预置的技能 |
| EA-03 | js-eyes-2-architecture-upgrade | 1.x 版本安装技能需经历下载 ZIP、解压、npm install、手动修改 openclaw.json、重启 OpenClaw 五步 |
| EA-04 | js-eyes-2-architecture-upgrade | 2.0.0 版本核心变更是将多子插件架构重构为“单一主插件 + 技能自动发现”模式 |
| EA-05 | js-eyes-2-architecture-upgrade | 2.0 架构中技能注册由主插件启动时扫描 skills/ 目录自动完成，不再需要手动配置 plugins.load.paths |
| EA-06 | js-eyes-2-architecture-upgrade | 2.0 架构移除了技能目录下的 openclaw-plugin/ 子目录，仅保留 skill.contract.js 作为唯一接口 |
| EA-07 | js-eyes-2-architecture-upgrade | 2.0 重构通过删除 24 个重复包装文件和收敛代码逻辑，总计减少 309 行代码 |
| EA-08 | js-eyes-2-architecture-upgrade | skill.contract.js 必须导出 createOpenClawAdapter 工厂函数，用于返回 OpenClaw 工具列表 |
| EA-09 | js-eyes-2-architecture-upgrade | 协议层新增 discoverLocalSkills 函数，用于扫描目录并识别所有包含 skill.contract.js 的子目录 |
| EA-10 | js-eyes-2-architecture-upgrade | 协议层 loadSkillContract 函数包含 require.cache 清除机制，以支持技能合约的热更新 |
| EA-11 | js-eyes-2-architecture-upgrade | 2.0 Monorepo 结构将 server、clients、cli 等模块全部收敛至 packages 目录下 |
| EA-12 | js-eyes-2-architecture-upgrade | 弃用子插件模式是因为其导致维护成本高、用户配置负担重、依赖关系混乱及启停状态分散 |
| EA-13 | js-eyes-2-architecture-upgrade | Skill Contract 模式的核心优势是将架构逻辑从“用户告诉系统有什么”转变为“系统自动发现有什么” |
| EA-14 | js-eyes-2-architecture-upgrade | 向后兼容通过 getLegacyOpenClawSkillState 函数读取旧配置并自动迁移至新配置的 skillsEnabled 字段实现 |
| EA-15 | js-eyes-2-architecture-upgrade | 目前已发布 8 个扩展技能，覆盖 X.com、Bilibili、YouTube、知乎、小红书、微信公众号、Reddit 和即刻 |
| EA-16 | js-eyes-2-architecture-upgrade | JS-Eyes 2.0 系统合计提供 17 个 AI 工具，包含 9 个主插件内置基础工具和 8 个扩展技能提供的工具 |
| EA-17 | js-eyes-2-architecture-upgrade | 技能安装命令统一为通过 curl 执行远程 install.sh 脚本并传入 skillId 参数 |
| EA-18 | js-eyes-2-architecture-upgrade | 2.0 架构将能力天花板从“已开发技能数”提升至“有人写过合约的网站数”，彻底打开了生态边界 |
| EA-19 | js-eyes-2-architecture-upgrade | 叙事角度应从工程配置简化转向“龙虾能力边界打开”，强调自动发现带来的生态扩展性 |
| EA-20 | js-eyes-2-architecture-upgrade | 未来降低新技能开发门槛的关键在于提供通用的技能合约模板 |

## 组内逻辑顺序

遵循“问题诊断 (1.x 痛点) -> 架构重构 (2.0 核心机制) -> 协议细节 (合约与发现) -> 迁移兼容 -> 生态价值”的逻辑顺序。首先阐述旧架构的冗余与局限 (EA-01~03, EA-12)，接着介绍 2.0 的核心变更与目录结构 (EA-04~07, EA-11)，深入协议层的自动发现与热更新机制 (EA-08~10, EA-13)，说明向后兼容方案 (EA-14) 与安装简化 (EA-17)，最后总结生态边界的打开与未来展望 (EA-15~16, EA-18~20)。
