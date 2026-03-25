# G80: AI 生成工作流将设计定义从“精确执行”重构为“对话策展”，需建立“视觉反馈→语言修正”的闭环以解决风格一致性难题

> 在时间紧迫的素材生产场景中，放弃传统模板化路径，转而采用“固定参数基座 + 分层对话迭代”的实时生成策略，是平衡效率与品牌调性的唯一解。

## 包含的 Atoms

| 编号  | 来源                     | 内容摘要 |
| ----- | ------------------------ | -------- |
| HD-01 | kl11-hackthon-recruit-design | 任务目标：当天产出 Agent Evolution Hackthon 招募海报与视频首帧 |
| HD-02 | kl11-hackthon-recruit-design | 策略决策：放弃设计师/模板，直接使用 JS_Claw+ComfyUI 实时交互生成 |
| HD-03 | kl11-hackthon-recruit-design | 风险识别：直接复用旧提示词会导致风格偏离、细节失控及记忆偏差 |
| HD-04 | kl11-hackthon-recruit-design | 一致性原则：必须保留种子、权重、负面词等完整生成参数 |
| HD-05 | kl11-hackthon-recruit-design | 核心方法论：建立“视觉反馈→语言修正”循环解决自然语言歧义 |
| HD-06 | kl11-hackthon-recruit-design | 迭代战术：采用对话式迭代法，每轮聚焦一个维度进行修正 |
| HD-07 | kl11-hackthon-recruit-design | 细节控制：面部调整需逐层剥离（如护目镜→颜色→移除），不可一步到位 |
| HD-08 | kl11-hackthon-recruit-design | 角色塑造：发色是性格外化关键（蓝黑=元气，银黑=高冷），姿态服装区分个性 |
| HD-09 | kl11-hackthon-recruit-design | 视觉策略：海报选择无面具全脸设计，降低神秘感以提升亲和力 |
| HD-10 | kl11-hackthon-recruit-design | 品牌隐喻：JS Logo 置于胸口能量核心，符合"能量在内心"的 Cyber-Taoist 理念 |
| HD-11 | kl11-hackthon-recruit-design | 色彩避坑：选择自然肤色，避免黄色皮肤带来的"黄疸"或过度"金属感"联想 |
| HD-12 | kl11-hackthon-recruit-design | 风格选型：攻壳×阿基拉漫画风优于写实风，契合 Neo-Brutalism 品牌调性 |
| HD-13 | kl11-hackthon-recruit-design | 工作流配置：使用 `image_z_image_turbo.json`，9 步快生模式（约 4 秒/张） |
| HD-14 | kl11-hackthon-recruit-design | 固定参数：采样器 `res_multistep`、调度器 `simple`、CFG 1、步数 9 |
| HD-15 | kl11-hackthon-recruit-design | 负面提示词：明确排除 `photorealistic, 3D render, soft shadows, gradients` |
| HD-16 | kl11-hackthon-recruit-design | 颜色精度：描述必须精确到色值（如#FCD228），禁用通用词 |
| HD-17 | kl11-hackthon-recruit-design | 控制层级：面部控制分三层递进（去遮罩→定肤色→细表情） |
| HD-18 | kl11-hackthon-recruit-design | 归档规范：所有文件按时间戳自动归档至特定输出目录 |
| HD-19 | kl11-hackthon-recruit-design | 成果甄选：00217 为主视觉（4.5/5），00221-00224 为人群备选 |
| HD-20 | kl11-hackthon-recruit-design | 视频计划：基于优选首帧，使用 `video_wan2_2_14B_i2v.json` 制作 5 秒视频 |
| HD-21 | kl11-hackthon-recruit-design | 范式转变：设计定义从执行转向策展，从精确控制转向对话涌现 |

## 组内逻辑顺序

遵循“任务背景与策略决策 (HD-01~02) → 核心挑战与方法论构建 (HD-03~06) → 具体执行细节与参数规范 (HD-07~18) → 成果验收与范式总结 (HD-19~21)"的实战流程顺序。
