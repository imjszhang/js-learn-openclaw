# G89: 选题系统必须采用“飞书管状态-Prism 管素材-Agent 管执行”的三层分工架构以消除创作不确定性

> 通过将分散的灵感集中化管理，并明确各工具职责边界，将“写什么”的随机决策转化为基于列表的确定性执行。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| TS-01 | kl26-topic-system-setup  | 养虾日记选题管理系统由 OpenClaw 和飞书多维表格共同搭建而成 |
| TS-02 | kl26-topic-system-setup  | 选题想法散落各处（Flomo、Chrome、聊天记录）会导致管理混乱且难以追踪 |
| TS-03 | kl26-topic-system-setup  | 缺乏清晰的进度管理会导致无法知晓写作阶段、素材充足度及选题时效性 |
| TS-04 | kl26-topic-system-setup  | 素材记录（Journal）与最终产出脱节会导致知识库更新无法转化为实际文章 |
| TS-05 | kl26-topic-system-setup  | 选题管理系统采用三层架构：飞书多维表格（管理层）、知识库 Prism（素材层）、定时产出（执行层） |
| TS-06 | kl26-topic-system-setup  | 飞书多维表格的核心字段包括篇号、标题、核心痛点、读者心理、解决方案、选题类别、优先级和状态 |
| TS-07 | kl26-topic-system-setup  | 选题类别分为五种：解决痛点、疑问新知、避坑指南、盲区纠正、轻松好玩 |
| TS-08 | kl26-topic-system-setup  | 知识库中 Journal 文件路径规范为 `journal/YYYY-MM-DD/klxx-slug.md` |
| TS-09 | kl26-topic-system-setup  | 知识库中 KL 框架文件路径规范为 `pyramid/structure/P25-yangxia-series/tree/KLxx-slug.md` |
| TS-10 | kl26-topic-system-setup  | 产出文件路径规范为 `outputs/yangxia-series/xx.md` |
| TS-11 | kl26-topic-system-setup  | 使用 `knowledge_prism_bind_output` 命令绑定定时产出，需指定视角目录、模板和 KL 策略参数 |
| TS-12 | kl26-topic-system-setup  | 工作流闭环逻辑为：多维表格选题触发知识库 Journal 创作，生成 KL 框架后产出文章，发布后更新表格状态 |
| TS-13 | kl26-topic-system-setup  | 截至 KL19 发布时，系统共管理 25 个选题，其中已发布 17 篇，待写 7 篇 |
| TS-14 | kl26-topic-system-setup  | 优先级 P0 分配给当前正在创作的选题（如 KL26），P1 分配给近期计划，P2 分配给远期储备 |
| TS-15 | kl26-topic-system-setup  | 最佳实践是让每个工具做最擅长的事：飞书管状态进度，Prism 管深度素材，Agent 管自动化执行 |
| TS-16 | kl26-topic-system-setup  | 选题系统的本质是将“写什么”的不确定性转化为“从列表选一个”的确定性 |

## 组内逻辑顺序

遵循“问题背景 -> 架构设计 -> 详细规范 -> 运行逻辑 -> 实证数据 -> 核心哲学”的结构顺序。首先阐述分散管理的痛点 (TS-02~TS-04)，提出三层架构方案 (TS-01, TS-05)，接着定义具体的字段、分类及文件路径规范 (TS-06~TS-11)，描述闭环工作流 (TS-12)，展示当前运行数据 (TS-13~TS-14)，最后总结工具分工原则与系统本质 (TS-15~TS-16)。
