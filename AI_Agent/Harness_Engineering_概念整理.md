# Harness Engineering 概念整理

> 来源线索：菜鸟教程 AI Agent 页面 `https://www.runoob.com/ai-agent/harness-engineering.html`
>
> 记录说明：当前自动抓取该页面正文失败，因此本文不是逐段摘录或逐句翻译，而是基于该主题的原创整理版。后续如果能获取网页正文，应再进行一次原文校对与补充。

---

## 1. 一句话理解

Harness Engineering 可以理解为：

```text
为 AI Agent 构建一套可控、可观测、可评估、可回放的运行与验证环境。
```

它关心的不是单次 prompt 写得多好，而是 Agent 在真实任务中如何：

- 接收任务；
- 读取上下文；
- 调用工具；
- 产生中间状态；
- 接收反馈；
- 校验结果；
- 记录过程；
- 在失败时定位原因；
- 在必要时升级到更高成本的模型或人工复核。

---

## 2. Harness 与 Prompt / Agent 的区别

| 概念 | 主要作用 | 典型问题 |
|---|---|---|
| Prompt Engineering | 设计一次输入如何表达 | 怎么让模型更好回答？ |
| Context Engineering | 设计给模型什么上下文 | 模型应该看到哪些信息？ |
| Agent Engineering | 设计模型如何规划和调用工具 | Agent 如何完成多步任务？ |
| Harness Engineering | 设计 Agent 的运行、评估、约束和反馈系统 | 如何证明 Agent 做得对、可控、可复现？ |

关键区别：

```text
Agent 负责执行和编排。
Harness 负责约束、观察、评估和闭环改进。
```

所以 harness 不是 agent 本身，也不是必须等到 agent 阶段才需要。

---

## 3. Harness 的核心组成

一个完整 harness 通常包含以下部分：

### 3.1 Task specification

明确任务是什么、输入是什么、输出应该长什么样。

例如：

```text
从 normalized JSON 中抽取 document_role、version、parameter、table_field。
```

### 3.2 Context selection

决定给系统哪些上下文，不给哪些上下文。

例如：

```text
T04-1 只读取 10-20 个 normalized JSON 样本，不读取全量语料。
```

### 3.3 Tool access

规定可以调用哪些工具、不能调用哪些工具。

例如：

```text
T04-1 可以运行规则抽取脚本，但不能调用 LLM，也不能创建 Agent workflow。
```

### 3.4 State and memory

记录当前阶段、已完成产物、失败样例和下一步任务。

在本项目中，对应：

```text
TASK_STATE.md
outputs/*/reports
harness/cases/*.jsonl
harness/reports/*.json
```

### 3.5 Observability

记录运行过程，方便回放和定位问题。

例如：

```text
输入文件列表
抽取结果
通过/失败 case
错误原因
运行时间
LLM 调用次数与成本
```

### 3.6 Verification

用测试、规则、golden cases 或人工标注判断产物是否合格。

例如：

```text
schema 是否通过
source_anchor 是否存在
version 是否抽对
parameter 是否有证据
finding 是否能追溯到原文
```

### 3.7 Failure attribution

当结果错误时，判断错误来自哪里：

- 输入解析错误；
- schema 设计错误；
- 规则覆盖不足；
- LLM 判断错误；
- evidence 丢失；
- routing 升级策略错误。

### 3.8 Permission and intervention

控制系统能做什么、不能做什么，以及什么时候需要人工确认。

例如：

```text
不能修改 data_Processed/
不能全量调用 LLM
不能在 semantic_harness 没通过前进入异常检测
```

---

## 4. Harness 的最小公式

可以把 harness 简化为：

```text
Harness = cases + runner + oracle + metrics + report
```

含义：

- cases：评估样例；
- runner：自动运行被测模块；
- oracle：标准答案或判断依据；
- metrics：评分指标；
- report：结果报告和失败样例。

---

## 5. Harness 如何知道对错

Harness 本身不知道真理，它依赖外部判据。

常见判据包括：

### 5.1 人工标注 golden cases

人工确认少量样本的正确答案。

例如：

```json
{"case_id":"version_001","doc_id":"DHF_xxx","task":"version","expected":{"value":"V1.0"}}
```

### 5.2 结构性规则

不需要语义理解即可判断。

例如：

```text
JSON 必须符合 schema。
每个 block 必须有 source_anchor。
每个 semantic_unit 必须有 evidence。
```

### 5.3 证据反查

检查系统给出的结论是否能回到原文。

例如：

```text
抽出的 version = V1.0，则 evidence text 中必须能找到 V1.0。
```

### 5.4 bad case 回流

人工审核发现错误后，把错误样例加入 harness，防止下次再犯。

---

## 6. 在 DocReview 项目中的阶段映射

| 阶段 | Harness 目标 | Agent 是否必要 |
|---|---|---|
| T03 normalized JSON | 检查结构、重复、source_anchor、字段稳定性 | 不必要 |
| T04 semantic extraction | 检查语义抽取准确率、证据覆盖、schema 合法性 | 不必要，先规则 |
| T05 knowledge index | 检查查询是否能找回正确实体和证据 | 不必要 |
| T06 intra-document consistency | 检查单文件 finding 是否准确 | 复杂 case 可引入 LLM |
| T07 inter-document consistency | 检查跨文档冲突、追溯断链、参数漂移 | 复杂推理可能需要 Agent/LLM |
| T08 finding schema | 检查 finding 字段完整和证据可追溯 | 不必要 |
| T09/T10 pipeline/routing | 检查端到端结果、分级路由和成本 | 需要 Agent/routing |

核心原则：

```text
Rules first.
Harness measures accuracy.
LLM or Agent is introduced only when harness shows rules are insufficient or orchestration is needed.
```

---

## 7. 当前 T04-1 的推荐 harness 设计

### 7.1 目标

建立 `semantic_harness`，评估规则抽取器是否能稳定抽出：

- document_role；
- version；
- parameter 子集；
- table_field 子集；
- evidence anchor。

### 7.2 输入

```text
outputs/t03_hybrid_auto/normalized_full/
```

### 7.3 样例文件

```text
harness/cases/semantic_public.jsonl
```

### 7.4 输出报告

```text
harness/reports/semantic_sample_report.json
```

### 7.5 最小指标

```text
schema_valid_rate = 1.0
evidence_coverage = 1.0
document_role_accuracy >= 0.8
version_accuracy >= 0.8
parameter_precision 初期优先于 recall
```

---

## 8. 为什么 harness 对 Agent 很重要

Agent 的问题通常不是“能不能生成答案”，而是：

```text
它生成的答案是否可信？
是否能解释来源？
是否能回放？
失败时能否定位？
成本是否可控？
什么时候该升级？
```

Harness 正是用来回答这些问题的。

因此：

```text
没有 harness 的 Agent 只是会行动。
有 harness 的 Agent 才能被评估、被约束、被迭代。
```

---

## 9. 对学习路线的启发

学习 AI Agent 时，不应该只学：

```text
prompt
function calling
multi-agent
workflow
```

还要学习：

```text
任务定义
上下文选择
工具权限
运行日志
评估样例
golden answer
错误归因
成本统计
人工复核
版本化报告
```

这就是 Harness Engineering 的价值。

---

## 10. 后续待补充

如果后续能完整读取原始网页，应补充：

- 网页中的正式定义；
- 网页列出的 harness 组成部分；
- 网页给出的示例；
- 与本文整理内容的差异；
- 可直接迁移到 DocReview 项目的部分。
