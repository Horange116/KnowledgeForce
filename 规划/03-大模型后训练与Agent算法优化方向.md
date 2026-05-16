# 大模型后训练与 Agent 算法优化方向

## 1. 方向定位

大模型后训练与 Agent 算法优化方向是高门槛算法研究方向。

核心目标是：

> 通过后训练、偏好对齐、强化学习、数据构建和评测体系，提升大模型与 Agent 在复杂任务中的规划、推理、工具调用、执行成功率和泛化能力。

这个方向更接近算法工程和研究工程，不是当前短期主攻方向，但需要理解其基本概念，因为很多高阶 Agent 岗位都会要求 SFT、DPO、RL、Reward、Trajectory 和 LLM-as-a-Judge。

---

## 2. 典型岗位名称

- 大模型算法工程师
- Agent 算法工程师
- Post-training 工程师
- RL / Reward 工程师
- Agentic RL 研究工程师
- 大模型能力优化工程师
- 大模型训练与评测工程师
- 多模态智能体算法工程师

---

## 3. 典型业务场景

### 3.1 Agent 能力增强

- 自主规划 Planning
- 多步推理 Reasoning
- 工具调用 Tool Use
- RAG 增强生成
- 数据问答
- 文案生成
- 搜索总结
- Deep Research
- 图像连续编辑
- 长文本理解

### 3.2 模型后训练

- 指令微调 Instruction Tuning
- SFT
- RLHF
- DPO
- PPO
- RLAIF
- Agentic RL
- Reward Model
- 偏好数据构建
- Trajectory 构建

### 3.3 数据飞轮

- 训练数据构建
- 高质量数据挖掘
- Synthetic Data
- 蒸馏
- 业务反馈回流
- 模型效果迭代
- Data-centric AI

### 3.4 评测与对齐

- 自动化 Benchmark
- LLM-as-a-Judge
- 复杂多步任务量化评估
- 偏好对齐
- 模型效果归因
- 离线评测与在线指标联动

---

## 4. 核心技术能力

### 4.1 大模型基础

- Transformer
- Attention
- Tokenizer
- Pretraining
- Instruction Tuning
- Alignment
- Scaling Law 基础
- 主流 LLM 架构
- Qwen / Llama / DeepSeek / GLM 等模型理解

### 4.2 后训练方法

- SFT
- LoRA / QLoRA
- DPO
- PPO
- RLHF
- RLAIF
- Reward Model
- Preference Learning
- Agentic RL
- Imitation Learning

### 4.3 训练与推理框架

- Python
- PyTorch
- TensorFlow 基础
- HuggingFace Transformers
- PEFT
- DeepSpeed
- Megatron-LM
- vLLM
- 分布式训练基础
- 推理加速基础

### 4.4 数据构建

- 指令数据构建
- 偏好数据生成
- Synthetic Data
- Trajectory 数据
- 数据清洗
- 数据筛选
- 数据质量评估
- 数据闭环

### 4.5 评测体系

- LLM-as-a-Judge
- Agent Benchmark
- 复杂任务评测
- 多步推理评测
- 工具调用成功率
- 执行成功率
- 逻辑一致性
- 泛化能力
- 评测结果归因

---

## 5. 和 Agent 应用工程的区别

| 维度 | Agent 应用工程 | 后训练与算法优化 |
|---|---|---|
| 目标 | 做出能落地的 Agent 应用 | 提升模型与 Agent 能力上限 |
| 主要工作 | RAG、Tool Calling、Workflow、服务化 | SFT、DPO、RL、Reward、数据构建 |
| 产出 | 应用系统、API、报告、评测 Harness | 模型、训练数据、评测集、算法报告 |
| 门槛 | 工程实现与业务理解 | 数学、深度学习、论文、训练经验 |
| 短期优先级 | 高 | 中低 |

---

## 6. 推荐学习顺序

### 阶段一：理解概念

- Transformer
- SFT
- RLHF
- DPO
- PPO
- Reward Model
- Agentic RL
- LLM-as-a-Judge

目标不是马上训练大模型，而是能读懂 JD 和论文摘要。

### 阶段二：做小规模 SFT / LoRA

完成：

- 构造小型指令数据集
- 使用 HuggingFace + PEFT
- LoRA 微调
- 保存 Adapter
- 推理对比
- 写实验报告

### 阶段三：做评测集与 Judge

完成：

- 构建 Agent 任务评测集
- 使用规则评测
- 使用 LLM-as-a-Judge
- 对比不同 Prompt / RAG 参数 / 模型
- 分析失败案例

### 阶段四：理解偏好数据与 DPO

完成：

- 构造 chosen / rejected 数据
- 理解 DPO 训练格式
- 跑通小模型 DPO 示例
- 对比 SFT 与 DPO 差异

### 阶段五：理解 Agentic RL

学习：

- Reward 设计
- Trajectory 数据
- 工具调用轨迹
- 任务成功率优化
- 多步任务评估

暂时不要求完整复现大规模 RL。

---

## 7. 推荐项目

### 7.1 LoRA / SFT 小模型实践

目标：完成一个可复现的小规模微调项目。

内容：

- 指令数据集构造
- LoRA 微调
- 微调前后对比
- 推理脚本
- 实验记录

### 7.2 Agent 评测集与 LLM-as-a-Judge

目标：为企业文档审查 Agent 构建评测体系。

内容：

- 多步任务样例
- 标准答案
- 引用标准
- Judge Prompt
- 自动打分
- 失败归因

### 7.3 Synthetic Data for Agent

目标：为 Agent 任务自动生成训练或评测数据。

内容：

- 生成用户问题
- 生成复杂多文档场景
- 生成工具调用轨迹
- 生成标准报告
- 人工抽样检查质量

### 7.4 Trajectory 数据构建 Demo

目标：记录 Agent 从输入到输出的完整执行轨迹。

内容：

- 用户任务
- Agent 计划
- 工具调用序列
- 中间观察
- 最终输出
- 成功 / 失败标签

---

## 8. 简历关键词

- Transformer
- SFT
- LoRA
- DPO
- PPO
- RLHF
- RLAIF
- Reward Model
- Agentic RL
- Preference Data
- Trajectory
- Synthetic Data
- Data-centric AI
- LLM-as-a-Judge
- Benchmark
- PyTorch
- HuggingFace
- DeepSpeed
- vLLM
- Megatron-LM
- Model Evaluation

---

## 9. 当前阶段建议

短期不要把主时间投入：

- 从零训练大模型
- 大规模分布式训练
- 完整 RLHF pipeline
- 复杂 PPO / GRPO 复现
- CUDA 内核优化
- 系统性论文复现

当前更合理的做法：

1. 先完成 Agent 应用工程项目。
2. 在项目中积累评测集、Trace、失败案例。
3. 用这些数据理解 LLM-as-a-Judge、Synthetic Data、Trajectory。
4. 再做一个小规模 LoRA / SFT / DPO 实验。
5. 最后再考虑 Agentic RL。

---

## 10. 投递建议

适合中长期投递：

- Agent 算法工程师
- 大模型后训练工程师
- 大模型评测工程师
- Agentic RL 研究工程师
- 大模型能力优化工程师

当前如果没有论文、训练经验或算法竞赛背景，不建议主投：

- 基础模型训练岗
- 强化学习研究岗
- AGI Agent 能力上限研究岗
- Seed / 基础研究团队高阶算法岗