# Codex Prompt: 搜索语音 / 音频模型 CoT 相关论文

你现在要帮我在本地项目中完成一次论文调研任务，主题是：**模型 Chain-of-Thought / reasoning 与语音、音频大模型相关的研究**。

## 任务目标

请围绕 Google Scholar / arXiv / Semantic Scholar / Papers with Code / GitHub 等公开来源，搜索并整理与以下主题相关的论文：

- Chain-of-Thought in audio language models
- Chain-of-Thought in speech LLMs
- audio reasoning / speech reasoning
- large audio-language model reasoning
- speech large language model reasoning
- audio CoT prompting
- speech CoT fine-tuning
- structured CoT data for audio reasoning
- reinforcement learning for audio/speech reasoning
- tool-augmented audio CoT / audio reasoning agents

重点优先级：

1. **和 speech / spoken language / ASR / speech LLM 直接相关的 CoT 工作**。
2. 其次是 general audio / music / sound reasoning 中和 CoT、reasoning process、thinking step 相关的工作。
3. 再其次是多智能体、工具调用、test-time reasoning、RL reasoning 等与音频推理有关的工作。

## 推荐检索关键词

请优先使用以下关键词组合搜索：

```text
"audio chain-of-thought"
"speech chain-of-thought"
"chain-of-thought" "large audio language model"
"chain-of-thought" "speech LLM"
"speech large language model" "reasoning"
"audio language model" "reasoning process"
"large audio-language model" "reinforcement learning" "reasoning"
"spoken reasoning tasks" "chain-of-thought"
"speech LLM" "thinking while listening"
"audio reasoning" "chain-of-thought"
"audio CoT"
"speech CoT"
"structured CoT" "audio reasoning"
"tool augmented" "audio reasoning"
```

## 已知重要论文种子

请至少检索、确认并整理以下论文：

1. **Audio-CoT: Exploring Chain-of-Thought Reasoning in Large Audio Language Model**
   - 重点：CoT prompting 是否提升 LALM 的 reasoning；sound/music/speech 三类任务；easy/medium/hard 上的差异；CoT 是否可能在 hard tasks 上造成混乱。

2. **Can Speech LLMs Think while Listening?**
   - 重点：speech LLM 中的 CoT fine-tuning；thinking while listening；spoken reasoning tasks；准确率和语音响应延迟 trade-off。

3. **Chain-of-Thought Prompting for Speech Translation**
   - 重点：语音翻译中的 CoT；ASR transcript 作为中间推理/中间表示；speech-to-text task 中 CoT 的形式。

4. **Audio-Reasoner: Improving Reasoning Capability in Large Audio Language Models**
   - 重点：CoTA 数据集；structured CoT process；通过 CoT 数据训练音频推理模型；和 Mellow / ReasonAQA 的关系。

5. **Audio-Thinker: Guiding Audio Language Model When and How to Think via Reinforcement Learning**
   - 重点：RL 训练音频模型什么时候思考、怎么思考；reasoning reward；audio reasoning。

6. **Thinking with Sound: Audio Chain-of-Thought Enables Multimodal Reasoning in Large Audio-Language Models**
   - 重点：把 CoT 和音频工具结合；噪声抑制、声源分离、时间对齐等 audio-domain analysis；不是只生成文本推理链。

7. **CESAR: Incentivizing Consistent, Effective and Scalable Reasoning Capability in Audio LLMs via Reasoning Process Rewards**
   - 重点：reasoning process reward；音频模型中长 CoT 可能导致错误累积；如何奖励有效推理。

8. **Audio-DeepThinker: Progressive Reasoning-Aware Reinforcement Learning for High-Quality Chain-of-Thought Emergence in Audio Language Models**
   - 重点：RL 让 audio CoT 涌现；progressive curriculum；reasoning similarity reward。

如果检索中发现这些论文不存在、标题变体不同、或只是 arXiv 未正式发表，请在结果中明确标注。

## 需要输出的文件

请在仓库中创建以下文件：

```text
语音论文/research_maps/audio_speech_cot_papers.md
```

## 输出内容结构

请在 `audio_speech_cot_papers.md` 中按以下结构整理：

```markdown
# 语音 / 音频模型 CoT 与推理相关论文调研

## 1. 调研目标

简要说明本次调研关注什么：speech/audio LLM 中的 CoT、reasoning process、thinking、test-time reasoning、RL reasoning、tool-augmented reasoning。

## 2. 总体分类

用表格把论文分成几类：

| 类别 | 代表论文 | 核心问题 | 与语音关系 | 优先级 |
|---|---|---|---|---|
| Audio CoT prompting | ... | ... | ... | 高/中/低 |
| Speech LLM CoT | ... | ... | ... | 高 |
| Speech translation CoT | ... | ... | ... | 高 |
| Structured CoT data | ... | ... | ... | 高 |
| RL for audio reasoning | ... | ... | ... | 中/高 |
| Tool-augmented audio reasoning | ... | ... | ... | 中 |
```

## 3. 重点论文卡片

每篇论文单独建一个小节，格式如下：

```markdown
### 论文标题

- 年份：
- 作者：
- 链接：
- 代码 / 项目地址：如有
- 任务类型：speech / audio / music / sound / mixed
- 方法类型：CoT prompting / CoT SFT / RL / tool-augmented / multi-agent / transcript-as-intermediate
- 核心问题：
- 方法概括：
- 实验数据集 / benchmark：
- 关键结果：
- 主要局限：
- 和我们项目的关系：
- 推荐阅读优先级：高 / 中 / 低
```

## 4. 与已读论文的关系

请专门对比以下几篇已经在读的论文：

- Mellow: a Small Audio Language Model for Reasoning
- AudioGenie-Reasoner
- AudSemThinker

要求说明：

1. 哪些论文更像 Mellow：偏数据构造 / SFT / CoT 数据注入。
2. 哪些论文更像 AudioGenie-Reasoner：偏 test-time reasoning / tool use / multi-agent / evidence refinement。
3. 哪些论文更像 AudSemThinker：偏 semantic descriptor / thinking phase / structured reasoning。
4. 哪些论文最适合后续接着读。

## 5. 推荐阅读顺序

请给出适合初学者的阅读顺序，不要只按时间排序，而是按理解难度和项目价值排序。建议至少分三层：

1. 入门必读；
2. 进阶理解；
3. RL / reward / test-time scaling 深入。

## 6. 对项目的启发

请总结这些论文对我们后续项目的启发，重点回答：

- CoT 在语音 / 音频任务中到底可能以哪些形式存在？
- 语音任务中 transcript 是否可以看作一种中间推理步骤？
- CoT 是不是一定提升？什么时候可能导致噪声或错误累积？
- 如何设计一个 Mini Audio/Speech CoT benchmark？
- 如果之后要复现，应该先复现哪条路线？

## 7. 候选复现路线

请给出 2-3 条可落地路线，例如：

### 路线 A：Speech transcript as CoT intermediate

目标：用 ASR transcript 作为中间步骤，验证语音问答/语音翻译中 CoT 的作用。

### 路线 B：Audio CoT prompting benchmark

目标：比较 direct answer vs CoT prompting 在 audio QA / MMAU-mini 小样本上的表现。

### 路线 C：Tool-augmented audio reasoning

目标：借鉴 AudioGenie-Reasoner / Thinking with Sound，构建 caption + ASR + audio-QA + LLM planner 的简化推理系统。
```

## 质量要求

- 不要只列论文标题，要解释每篇论文解决什么问题。
- 每篇论文都要标注与 speech/audio 的关系，避免混入纯文本 CoT 论文。
- 对 arXiv 论文要标注 arXiv 链接和年份。
- 如果有 GitHub 代码，记录代码地址。
- 如果论文是最近工作，优先确认标题、作者、年份是否准确。
- 不要虚构结果；没查到的信息写“未确认”。
- 最终文件要中文撰写，结构清晰，适合我后续阅读。

## 完成后回复

完成后只回复：

```text
已完成语音/音频 CoT 论文调研并写入 MD。
```
