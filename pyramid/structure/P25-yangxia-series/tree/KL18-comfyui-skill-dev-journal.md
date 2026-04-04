# KL18 - 我的第一个 OpenClaw 技能：把 ComfyUI 变成一句话生图工具

> 核心论点：ComfyUI 很强大但也很复杂。通过开发 js-comfyui-skill，我把节点式工作流封装成一句话命令，让龙虾能帮我生成 Neo-Brutalist 风格配图。这篇文章教你如何把复杂的 AI 工具变成简单的 Skill。

---

## 支撑材料

### 选题背景
- **痛点**：ComfyUI 工作流太复杂，参数调整靠记忆，每次翻笔记找节点 ID
- **需求**：养虾日记需要大量 Neo-Brutalist 风格配图，手动操作效率低
- **方案**：开发 js-comfyui-skill，把常用工作流封装成 CLI，一条命令生成图片/视频
- **成果**：已生成 100+ 张配图，养虾日记 KL11-17 全部用它生成

### ComfyUI 简介
**ComfyUI** 是一个基于节点（Node）的 Stable Diffusion 图形化界面：
- **节点式工作流**：把生图过程拆成 Load Checkpoint → CLIP Text Encode → KSampler → VAE Decode → Save Image 等节点
- **可视化编辑**：拖拽连接节点，直观看到数据流向
- **工作流复用**：保存为 JSON 文件，下次直接加载
- **扩展性强**：支持自定义节点、模型、LoRA

**为什么选 ComfyUI？**
- 比 Midjourney 更可控（能调每个参数）
- 比 WebUI 更灵活（节点组合自由）
- 本地运行，数据隐私
- 开源免费

### Z-Image Turbo (Lumina2) 模型
**Z-Image** 是基于 **Lumina2** 的本地生图模型：

| 特性 | Z-Image Turbo | Z-Image (标准) |
|------|---------------|----------------|
| **步数** | 9 步 | 25-50 步 |
| **速度** | ~4 秒/张 | ~15-30 秒/张 |
| **质量** | 够用（草图/概念） | 高质量（成品） |
| **适用场景** | 快速迭代、批量生成 | 精细调整、最终输出 |
| **工作流文件** | `image_z_image_turbo.json` | `image_z_image.json` |

**技术原理**：
- Lumina2 使用 Flow Matching 而非传统扩散模型
- 少步数（9步）即可收敛，大幅降低推理时间
- 适合风格化生成（Neo-Brutalism、漫画、线稿）

**本地部署硬件配置要求**：

| 配置项 | 最低要求 | 推荐配置 | 我的配置 |
|--------|----------|----------|----------|
| **显卡** | GTX 1060 6GB | RTX 3060 12GB | **RTX 5090 32GB** |
| **显存** | 6GB | 8GB+ | **32GB** |
| **内存** | 16GB | 32GB | 32GB |
| **存储** | 10GB 可用空间 | SSD 50GB+ | SSD 200GB |
| **系统** | Windows 10 | Windows 11 | Windows 11 |

**不同显卡性能参考**（512x512 图片，Turbo 9步）：
- GTX 1060 6GB：~15 秒/张
- RTX 3060 12GB：~8 秒/张
- RTX 4070 12GB：~4 秒/张
- RTX 4090 24GB：~2 秒/张
- **RTX 5090 32GB：~1.5 秒/张**（我的配置，还能同时跑视频生成）

**显存优化技巧**：
- 使用 `--lowvram` 参数启动 ComfyUI（8GB 以下显卡）
- 使用 `--normalvram`（8-12GB 显卡）
- 关闭浏览器预览减少显存占用
- 批量生成时使用队列而非并发

**模型文件下载**：
- Z-Image Turbo 模型约 4.5GB
- 存放路径：`ComfyUI/models/checkpoints/z_image_turbo.safetensors`
- 下载地址：Hugging Face / 魔搭社区

### js-comfyui-skill 开发历程

#### Day 1-2：需求分析与 ComfyUI 入门
**Day 1：明确需求**
- 要解决的问题：ComfyUI 参数复杂，记不住节点 ID
- 定义 MVP：一条命令生成 Neo-Brutalist 风格配图
- 成功标准：不需要打开 ComfyUI 界面，不需要记节点 ID

**Day 2：ComfyUI 入门**
- 学习节点式工作流概念
- 理解 KSampler、CFG、Step 等参数含义
- 测试 Z-Image Turbo 工作流
- 关键发现：工作流可以保存为 JSON，通过 API 调用

#### Day 3-4：技术选型与架构设计
**技术方案对比**：
| 方案 | 优点 | 缺点 | 选择 |
|------|------|------|------|
| A. 直接调用 ComfyUI API | 灵活 | 需要自己处理文件上传、进度轮询、错误处理 | 放弃 |
| B. 封装成 Python 脚本 | 可控 | 用户需要装 Python 环境 | 放弃 |
| C. 做成 OpenClaw Skill | 集成度高、自然语言交互 | 需要学习 Skill 开发规范 | ✅ 选定 |

**架构设计**：
```
用户输入: "生成一张 Neo-Brutalist 风格的龙虾配图"
    ↓
OpenClaw Agent 解析意图
    ↓
js-comfyui-skill 接收参数 (prompt, workflow, size)
    ↓
调用 ComfyUI API (POST /prompt)
    ↓
轮询生成进度 (WS /ws)
    ↓
下载生成的图片
    ↓
返回给用户 (图片路径 + 预览)
```

#### Day 5-6：核心开发（工作流解析与参数注入）
**Day 5：工作流 JSON 解析**
- 问题：ComfyUI 工作流 JSON 中 Prompt 节点 ID 不固定
- 解决：遍历 JSON 找到 class_type 为 "CLIPTextEncode" 的节点
- 代码片段：
```javascript
function findPromptNode(workflowJson) {
  for (const [id, node] of Object.entries(workflowJson)) {
    if (node.class_type === "CLIPTextEncode") {
      return id;
    }
  }
}
```

**Day 6：参数注入**
- 实现将用户 prompt 注入到工作流 JSON 的指定节点
- 支持自定义尺寸、种子、步数等参数
- 产出：可运行的命令行原型

#### Day 7-8：API 调用与文件处理
**Day 7：ComfyUI API 封装**
- POST /prompt 提交工作流
- WebSocket 监听生成进度
- 处理生成完成的回调

**Day 8：文件上传与下载**
- 图生图场景需要上传参考图
- 实现图片自动上传到 ComfyUI 的 input 目录
- 下载生成的图片到指定目录

#### Day 9：多工作流支持
**工作流管理**
- 封装 5 个工作流：
  - `image_z_image.json` - 标准 Z-Image（25步高质量）
  - `image_z_image_turbo.json` - Turbo 快生（9步）
  - `image_z_image_turbo_fun_union_controlnet.json` - ControlNet 结构引导
  - `image_qwen_image_edit_2511.json` - Qwen 多图语义编辑
  - `video_wan2_2_14B_i2v.json` - Wan 2.2 图生视频

**CLI 设计**
```bash
# 文生图
comfyui generate --workflow turbo --prompt "Neo-Brutalist lobster"

# 图生图
comfyui transform --workflow controlnet --image input.png --prompt "same pose, cyberpunk style"

# 视频生成
comfyui video --workflow wan-i2v --image first.png --prompt "camera zoom in"
```

#### Day 10：打包发布与文档编写
**SKILL.md 编写**
- 技能描述：一句话让 ComfyUI 生成图片
- 使用场景：公众号配图、海报生成、概念设计
- 配置说明：ComfyUI 服务地址、默认工作流
- 使用示例：各种场景的命令示例

**ClawHub 上架**
- 打包 skill 目录
- 提交审核
- 审核反馈：需说明 ComfyUI 前置依赖
- 补充说明后通过

### 核心踩坑记录

**坑 1：工作流 JSON 中节点 ID 不固定**
- 现象：每次在 ComfyUI 界面保存，Prompt 节点 ID 都变
- 解决：通过 class_type 查找节点，而非硬编码 ID

**坑 2：图片上传路径问题**
- 现象：ComfyUI 只能读取 input 目录下的图片
- 解决：自动复制用户图片到 ComfyUI/input/ 目录

**坑 3：WebSocket 连接超时**
- 现象：大图片生成时间长，WebSocket 断开
- 解决：增加心跳检测，支持断线重连

**坑 4：显存不足导致生成失败**
- 现象：同时生成多张图时显存溢出
- 解决：添加队列机制，串行处理请求

### 使用数据
- **生成图片总数**：100+ 张（养虾日记 KL11-17 全部配图）
- **平均生成时间**：Turbo 模式 4 秒/张，标准模式 20 秒/张
- **风格一致性**：通过固定 seed + 风格提示词模板，实现 90%+ 一致性
- **月 Token 消耗**：$0（本地生成，零 API 成本）

### 工作流封装的核心价值

**Before（原生 ComfyUI）**：
1. 打开浏览器
2. 加载工作流 JSON
3. 找到 Prompt 节点
4. 输入提示词
5. 找到 KSampler 节点
6. 调整步数、CFG
7. 点击 Queue Prompt
8. 等待生成完成
9. 右键保存图片
10. 复制到文章目录

**After（js-comfyui-skill）**：
```bash
openclaw generate "Neo-Brutalist lobster, black background with yellow #FCD228 accents"
```

**封装带来的价值**：
- **时间节省**：从 2 分钟 → 5 秒
- **认知减负**：不需要记节点 ID、参数位置
- **可复用**：工作流配置一次，重复使用
- **可分享**：团队成员用同样的命令，得到同样的风格

---

## 逻辑结构

### 引子：配图地狱与 ComfyUI 的复杂度
- 场景：养虾日记需要大量 Neo-Brutalist 风格配图
- 问题：手动操作 ComfyUI 太麻烦，参数记不住
- 解决思路：把 ComfyUI 封装成一句话命令
- 悬念：如何把一个节点式工作流变成简单 CLI？

### 第一部分：ComfyUI 是什么？（入门介绍）
**节点式生图工作流**
- Load Checkpoint → CLIP Text Encode → KSampler → VAE Decode → Save Image
- 每个节点负责一个环节，数据流向清晰可见
- 对比：Midjourney（黑盒） vs ComfyUI（白盒可控）

**为什么选 ComfyUI？**
- 本地运行，数据隐私
- 参数全可控，适合风格化生成
- 工作流可保存复用

### 第二部分：Z-Image Turbo 模型（技术介绍）
**Lumina2 与 Flow Matching**
- 传统扩散模型：100 步去噪
- Flow Matching：9 步收敛，速度提升 10 倍+
- 适用场景：快速迭代、批量生成

**Turbo vs 标准模式对比**
| 维度 | Turbo (9步) | 标准 (25步) |
|------|-------------|-------------|
| 速度 | 4 秒 | 20 秒 |
| 质量 | 够用 | 精致 |
| 适用 | 草图/概念 | 成品 |

**Neo-Brutalist 生成参数**
- 提示词模板：`black background with yellow #FCD228 accents, 3px solid borders, hard shadows, 50px technical grid pattern`
- 负面提示词：`photorealistic, 3D render, soft shadows, gradients`
- 固定参数：CFG 1, Step 9, Sampler res_multistep

### 第三部分：10天开发实录

**Day 1-2：需求与选型**
- 明确 MVP：一句话生成配图
- 技术选型：直接 API vs Python 脚本 vs OpenClaw Skill

**Day 3-4：架构与原型**
- 设计数据流：用户输入 → Skill → ComfyUI API → 图片返回
- 实现工作流 JSON 解析与参数注入

**Day 5-6：API 封装与文件处理**
- POST /prompt 提交工作流
- WebSocket 轮询进度
- 图片上传下载处理

**Day 7-8：多工作流支持**
- 封装 5 个工作流：文生图、图生图、ControlNet、Qwen编辑、视频生成
- CLI 设计：简洁的命令行接口

**Day 9：踩坑与优化**
- 节点 ID 不固定 → class_type 查找
- 显存不足 → 队列串行处理
- WebSocket 超时 → 心跳重连

**Day 10：打包发布**
- SKILL.md 编写
- ClawHub 上架与审核

### 第四部分：工作流封装的核心思维

**从「界面操作」到「命令封装」**
- 识别高频操作序列
- 提取可变参数（prompt、image、size）
- 固化不变配置（模型、采样器、风格模板）

**从「给自己用」到「给别人用」**
- 文档比代码更重要
- 提供使用示例和故障排查指南
- 设计合理的默认值

### 第五部分：实际应用场景

**场景 1：养虾日记配图**
- 每篇文章 3-7 张图
- Turbo 模式快速迭代
- 风格一致性保障

**场景 2：海报生成**
- 固定尺寸 + 固定风格
- 批量生成多版本
- ControlNet 结构引导

**场景 3：视频制作**
- 图生视频（Wan 2.2）
- 首尾帧视频（Wan 2.2 flf2v）
- KL11 Hackthon 招募视频案例

### 结尾：封装是降维的艺术

ComfyUI 很强大，但也复杂。
js-comfyui-skill 的价值不在于替代 ComfyUI，而在于**封装复杂性**。

把专业工具变成一句话命令，让更多人能用上 AI 生图的力量。

这就是 Skill 开发的本质：**不是创造新技术，而是让现有技术更易用**。

---

## 附录：快速参考

### 安装 js-comfyui-skill
```bash
openclaw skill install js-comfyui-client
```

### 常用命令
```bash
# Turbo 快生（9步，4秒）
openclaw generate "Neo-Brutalist lobster" --workflow turbo

# 标准质量（25步，20秒）
openclaw generate "Neo-Brutalist lobster" --workflow standard

# 图生图
openclaw transform input.png --prompt "cyberpunk style" --workflow controlnet

# 视频生成
openclaw video first.png --prompt "camera zoom in" --workflow wan-i2v
```

### ComfyUI 前置要求
- 本地安装 ComfyUI（https://github.com/comfyanonymous/ComfyUI）
- 下载 Z-Image 模型（Lumina2）
- 启动 ComfyUI 服务（默认 http://127.0.0.1:8188）

### 工作流文件位置
```
~/.openclaw/skills/js-comfyui-client/workflows/
├── image_z_image.json
├── image_z_image_turbo.json
├── image_z_image_turbo_fun_union_controlnet.json
├── image_qwen_image_edit_2511.json
├── video_wan2_2_14B_i2v.json
└── video_wan2_2_14B_flf2v.json
```

---

## 引用

### Synthesis（顶层观点）
- S45: 技能（Skill）是能力扩展的核心机制
- S61: AI 生成工作流需将设计定义从「精确执行」重构为「对话策展」

### Groups（中层分组）
- G34: JS-Eyes 浏览器自动化技能
- G73: OpenClaw 技能/插件扩展机制
- G80: Hackthon 视觉设计工作流（Z-Image Turbo 应用）

### Atoms（底层信息单元）
- HD-13 至 HD-21: Hackthon 设计相关的 atoms（Z-Image Turbo 工作流配置）
- （待补充 comfyui-skill 开发过程的 journal atoms）

---

## 写作要点

1. **技术解释清晰**：ComfyUI 节点式工作流需要图解或类比说明
2. **对比数据说话**：Turbo vs 标准模式的速度/质量对比
3. **开发过程真实**：记录踩坑过程，这是最有价值的部分
4. **场景化呈现**：用养虾日记配图的实际案例说话
5. **可操作性强**：给出具体的命令、参数、配置文件

## 状态

- [ ] 素材收集中（需要补充详细的开发日记和代码片段）
- [ ] 确认 ComfyUI 技术细节（版本、模型下载链接）
- [ ] 确认 ClawHub 上架信息
- [ ] 待写作

---

## 需要的补充素材

1. **ComfyUI 界面截图**：节点式工作流的直观展示
2. **工作流 JSON 示例**：image_z_image_turbo.json 的关键部分
3. **开发过程的 Git commit 记录**：证明真实的开发时间线
4. **生成的配图示例**：展示 Neo-Brutalist 风格效果
5. **Skill 使用截图**：命令行调用和结果展示
6. **ClawHub 上架截图**：审核和发布证明
