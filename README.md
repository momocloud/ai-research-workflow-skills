# AI Research Workflow Skills

一套面向深度学习科研的 AI 辅助研究工作流 skills，适配 Claude Code、OpenAI Codex CLI 和 OpenCode。

## 灵感来源

- [AI科研不完全指北：Top10 PhD烧了上万刀的真实经验。 Context Is All You Need](https://linux.do/t/topic/1969598)
- [AI 科研不完全指北第二期：如何Propose一个idea](https://linux.do/t/topic/2005563)

核心论点：**Context Is All You Need。** 被污染的上下文会崩溃式地影响模型能力。整个工作流的所有设计决策，背后都只在做一件事：管住每一个 session 的上下文。

本项目由 Claude Code + GLM-5.1 协作实现。

---

## 整体架构

```mermaid
flowchart TD
    A["文献调研 Survey"] --> B["确定 Baseline"]
    B --> C["复现 Baseline"]
    C --> D["提出 Idea"]
    D --> E["Research Contract"]
    E --> F["跑实验"]
    F --> G["分析结果"]
    G -->|"不通过"| D
    G -->|"通过"| H["写论文"]
```

## 工作流阶段

| 阶段 | 命令 | 说明 | 核心产出 |
|------|------|------|----------|
| 1 | `/research-survey` | 文献调研 | 结构化综述文档 |
| 2 | `/research-baseline` | 确定 baseline | 任务设定 + baseline 规格 |
| 3 | `/research-reproduce` | 复现 baseline | 验证后的指标 + 干净的环境路径 |
| 4 | `/research-idea` | 提出并评估 idea | 排序后的 idea cards |
| 5 | `/research-contract` | 写研究合约 | 不可变的实验合约 |
| 6 | `/research-experiment` | 跑实验 | 实验结果 + 原始数据 |
| 7 | `/research-analyze` | 分析结果 | 逐信号评估报告 |
| 8 | `/research-write` | 写论文 | 论文草稿 |

阶段 4-7 是迭代循环。好的 idea 从实验信号中生长出来，不要指望一次就完美。

---

## 上下文管理架构

这是整个工作流最核心的设计。每个阶段的上下文必须严格隔离：

```mermaid
flowchart TB
    subgraph main["主线程（干净上下文）"]
        M1["只做：推理、决策、协调<br/>不做：安装、下载、训练、日志输出"]
    end
    M1 -.- S1
    M1 -.- S2
    M1 -.- S3
    subgraph sub["Sub-agent / 后台任务 — 脏活都在这里跑，结果以摘要形式返回主线程"]
        S1["pip 安装 / 环境配置"]
        S2["数据下载 / 校验验证"]
        S3["模型训练 / tmux 会话"]
    end
```

**规则：如果模型反复修不好一个 bug，不一定是它没能力，而是上下文已经被彻底带偏了。重开一个干净的 session。**

---

## Research Contract 机制

合约是整个实验阶段的唯一准绳，防止事后合理化：

```mermaid
flowchart LR
    subgraph contract["Research Contract v1"]
        A["Hypothesis<br/>可证伪的假说"]
        B["Method<br/>方法概述"]
        C["Success Signals<br/>具体可量化的成功标准"]
        D["Failure Signals<br/>独立定义的失败信号"]
        E["Ablation Plan<br/>每个消融实验测试什么、预期结果"]
    end
```

时间线：

```mermaid
flowchart LR
    T1["合约签署 — 不可修改"] --> T2["实验开始 — 任何偏差必须立即上报"] --> T3["结果分析"]
```

⚠️ 实验开始后不可修改，只能版本升级（v1 → v2）

### 合约完整性校验

实验开始时自动锁定合约，防止事后篡改：

```
.research/                   ← 自动创建，加入 .gitignore
  contracts/
    v1.lock.md               ← 实验开始时的合约快照
  v1.sha256                  ← SHA256 校验值
```

```mermaid
flowchart LR
    A["/research-experiment<br/>启动时：计算 SHA256<br/>存储快照到 .research/"] --> B["/research-analyze<br/>启动时：比对 SHA256<br/>一致则继续"]
    B --> C["/research-write<br/>启动时：比对 SHA256<br/>一致则继续"]
    A -.->|"不一致"| D["STOP<br/>显示 diff<br/>用户决定：回滚或升级 v2"]
    B -.->|"不一致"| D
    C -.->|"不一致"| D
```

所有操作仅使用 POSIX 标准工具（`sha256sum`、`awk`、`diff`），无需额外依赖。

---

## 多模型协作架构

不同模型有不同优势，整个工作流利用模型间的 ensemble 效应：

```mermaid
flowchart TB
    User["用户（Human）<br/>关键决策点拍板"]
    User --> Gemini
    User --> Claude
    User --> GPT

    subgraph models["多模型协作"]
        Gemini["Gemini<br/>搜索能力最强 / 知识面最广 / 适合发散"]
        Claude["Claude<br/>代码最可靠 / 结构化思维 / 适合实现"]
        GPT["GPT/Codex<br/>推理判断最准 / 适合独立评审 / 适合分析"]
    end

    Gemini --> G1["文献调研 / Idea发散 / 竞品检查"]
    Claude --> C1["代码实现 / 架构决策 / 论文写作"]
    GPT --> P1["Idea评审 / 结果分析 / 独立Review"]
```

协作流程（以 Idea 阶段为例）：

```mermaid
flowchart LR
    A["Gemini（发散）<br/>生成10个候选 idea<br/>干净上下文"] --> B["Codex（评审）<br/>逐个独立评分+判定<br/>GO/REVISE / 隔离评审"] --> C["Claude（拍板）<br/>综合决策选定最终 idea<br/>处理REVISE"]
```

---

## 核心原则

```mermaid
mindmap
  root((核心原则))
    Context Is All You Need
      上下文被污染 → 重开 session
      不要在脏上下文里死磕
    No Fabrication
      所有结论必须有出处
      所有数字来自真实代码
    Context Isolation
      脏活在 sub-agent 里跑
      主线程只做推理
    Research Contract 不可变
      实验前定义成功/失败标准
      实验后不允许修改
    Ideas Grow from Experiments
      Idea 从实验信号中长出来
      不是凭空想出来的
    Human in the Loop
      AI 提议，人拍板
      环境搭建、idea选择、结果解读
    Multi-Model Ensemble
      不同模型做不同的事
      交叉 review 是免费的提升
    优先 LaTeX 源文件而非 PDF
      模型不会真正从头到尾读 PDF
    脏活在后台跑
      pip install、训练、下载
      永远不要在主线程
    专注顶会论文 + 有代码
      CVPR/ICCV/NeurIPS/ICML/ACL
      有代码才可复现
```

---

## 典型工作流

### 工作流 A：从零开始的新项目

最常见的场景：你对一个方向感兴趣，但还没有明确的 idea 和代码。

```mermaid
flowchart LR
    A1["Day 1<br/>/research-survey<br/>搜集30+篇相关论文<br/>识别趋势和gap"] --> A2["Day 2-3<br/>/research-baseline<br/>选定任务设定和baseline"]
    A2 --> A3["Day 4-7<br/>/research-reproduce<br/>后台跑训练 / 验证指标"]
    A3 --> C1
    subgraph loop["Day 8-14 迭代循环"]
        C1["/research-idea"] --> C2["/research-contract"]
        C2 --> C3["/research-experiment"]
        C3 --> C4["/research-analyze"]
        C4 -->|"迭代 2-4 轮"| C1
    end
    C4 -->|"通过"| D["/research-write<br/>写论文"]
```

**操作示例（Claude Code）：**

```bash
# Day 1: 调研
/research-survey
> 研究方向：3D点云目标检测中的高效特征提取方法

# Day 2: 确定 baseline
/research-baseline
> 基于 survey 结果，选定 PointNeXt (CVPR 2023) 作为 baseline

# Day 4: 复现（在新的干净 session 中）
/research-reproduce
> Baseline: PointNeXt, 数据集: ScanNet, 指标: mAP@0.5

# Day 8: 开始迭代
/research-idea
> 基于 survey + 复现结果生成候选 idea

# 选定 idea 后
/research-contract
> 为选定的 idea 写合约

# 合约确认后
/research-experiment
> 严格按合约跑实验

# 实验完成后
/research-analyze
> 逐信号评估

# 如果通过
/research-write
> 写论文草稿
```

---

### 工作流 B：在已有项目上迭代改进

你已经有一个 running pipeline，想进一步提升性能或尝试新方向。

```mermaid
flowchart LR
    A["你已有的<br/>running code + baseline<br/>复现结果"] --> B["/research-survey<br/>针对性搜索"]
    B --> C["/research-idea<br/>基于已有实验信号生成idea"]
    C --> D["/research-contract"]
    D --> E["/research-experiment"]
    E --> F["/research-analyze"]
    F -->|"通过"| G["/research-write"]
    F -->|"不通过"| H["修改合约v2或<br/>换idea重新迭代"]
```

**关键区别：**
- 跳过 baseline 确定和复现阶段（你已经有）
- survey 阶段更有针对性：搜索当前方法没能覆盖的相关工作
- idea 阶段可以利用已有实验的失败信号作为上下文

**操作示例：**

```bash
# 在你已有的项目目录中启动新 session
# 直接告诉 AI 你当前的状况：
/research-idea
> 我目前 follow 了一篇 CVPR 2026 的论文，在 ScanNet 上
> 复现了他们的结果（mAP=63.2），尝试加 attention module
> 后提升到 65.1，但训练不稳定，loss 在 epoch 80 后开始抖动。
> 请基于这些信号生成改进 idea。

# 或先做针对性调研
/research-survey
> 搜索：3D目标检测中解决训练不稳定的方法，以及
> attention module 在点云特征提取中的最新工作

# 然后走合约 → 实验 → 分析的流程
```

---

### 工作流 C：探索式研究（无明确 baseline）

你想进入一个新领域，还没有明确的目标论文和 baseline。

```mermaid
flowchart LR
    A["/research-survey<br/>广泛搜索 / 建立领域认知"] --> B["阅读核心论文<br/>方法+实验<br/>人工深度阅读"]
    B --> C["/research-survey 第二轮<br/>带着问题搜"]
    C --> D["/research-baseline<br/>基于深度阅读选定方向"]
    D --> E["进入工作流 A<br/>从 reproduce 开始"]
```

**关键区别：**
- 两轮 survey：第一轮广泛扫描建立认知，人工深度阅读 3-5 篇核心论文，第二轮带着具体问题搜索
- Human in the loop 更重：你需要自己读论文的关键细节（作者遇到的困难、决策过程），这些信息 AI 很难直接获取
- baseline 选择更谨慎：你对领域的理解决定了 baseline 的质量

**操作示例：**

```bash
# 第一轮：广泛扫描
/research-survey
> 研究方向：图神经网络在分子性质预测中的应用

# ... 人工阅读 survey 推荐的 top 3-5 论文 ...

# 第二轮：带着问题搜索
/research-survey
> 基于我对 SchNet 和 DimeNet 的阅读，它们在处理
> 长程分子相互作用时有明显局限。搜索：
> 1. 解决长程相互作用的 GNN 方法
> 2. 等变神经网络在分子预测中的最新进展
> 3. 3D分子表示学习中 position encoding 的新方法

# 确定 baseline
/research-baseline
> 基于我的阅读和第二轮 survey，选定 EquiformerV2
> (ICLR 2024) 作为 baseline

# 之后进入工作流 A 的 reproduce 阶段
```

---

### 工作流 D：快速验证一个 idea（最轻量）

你已经有一个明确的 idea，只想快速验证可行性，不需要完整的论文产出。

```mermaid
flowchart TD
    A["手动准备（5-10 分钟）<br/>确认 baseline 代码可跑<br/>确认数据路径 / 确认环境干净"] --> B["/research-contract<br/>写一个精简合约<br/>只定义1-2个核心成功信号"]
    B --> C["/research-experiment<br/>只跑主实验，跳过 ablation<br/>用最少的 epoch 验证趋势"]
    C --> D["/research-analyze"]
    D -->|"核心信号通过"| E["展开完整流程"]
    D -->|"核心信号不通过"| F["kill idea"]
```

**关键区别：**
- 手动准备环境，避免 AI 环境搭建的上下文污染
- 精简合约，快速验证
- 只跑主实验，不做 ablation
- 如果信号 positive，再回到工作流 A/B 展开完整流程

---

### 工作流选择指南

```mermaid
flowchart TD
    Start["你的起点是什么？"]
    Start --> A["我对一个方向感兴趣<br/>但没有idea"]
    Start --> B["我有代码和baseline<br/>但想改进"]
    Start --> C["我想进入新领域"]
    Start --> D["我有个 idea<br/>想快速试"]
    A --> WA["工作流 A：从零开始"]
    B --> WB["工作流 B：迭代改进"]
    C --> WC["工作流 C：探索式"]
    D --> WD["工作流 D：快速验证"]
```

---

## 部署方式

### Claude Code

```bash
cp -r claude-code/.claude /your/project/
```

在项目中创建 `.claude/commands/research-*.md`，通过 `/research-survey`、`/research-baseline` 等调用。

### OpenAI Codex CLI

```bash
cp codex/AGENTS.md /your/project/
cp -r codex/.agents /your/project/
```

Codex 自动发现 `.agents/skills/research-*/SKILL.md`。

### OpenCode

```bash
cp opencode/AGENTS.md /your/project/
cp -r opencode/.opencode /your/project/
```

OpenCode 也兼容 Claude Code 模式，可以直接用 Claude Code 的 commands：

```bash
cp -r claude-code/.claude /your/project/
```

### 文件结构对比

**Claude Code：**

```
.claude/commands/
  ├── research-survey.md      ← slash command
  ├── research-baseline.md
  ├── research-reproduce.md
  ├── research-idea.md
  ├── research-contract.md
  ├── research-experiment.md
  ├── research-analyze.md
  └── research-write.md
```

**Codex CLI：**

```
AGENTS.md                    ← 跨切面原则
.agents/skills/
  ├── research-survey/SKILL.md
  ├── research-baseline/SKILL.md
  ├── ...
  └── research-write/SKILL.md
```

**OpenCode：**

```
AGENTS.md                    ← 跨切面原则
.opencode/skills/
  ├── research-survey/SKILL.md
  ├── research-baseline/SKILL.md
  ├── ...
  └── research-write/SKILL.md
```

---

## 阶段间依赖关系

```mermaid
flowchart TD
    A["research-survey<br/>文献调研"] -->|"依赖"| B["research-baseline<br/>确定 baseline"]
    B -->|"依赖"| C["research-reproduce<br/>复现 baseline"]
    C -->|"依赖"| D["research-idea<br/>提出 + 评估 idea"]
    D -->|"依赖"| E["research-contract<br/>写研究合约"]
    User["用户确认选定的 idea"] --> E
    E -->|"依赖"| F["research-experiment<br/>跑实验"]
    F -->|"依赖"| G["research-analyze<br/>分析结果"]
    G -->|"通过"| H["research-write<br/>写论文"]
    G -->|"不通过"| D
    H --> I["论文草稿"]
```
