# Harness Engineering（驾驭工程）概念整理

> 来源：菜鸟教程 AI Agent 教程《Harness Engineering（驾驭工程）》页面粘贴文本。
>
> 整理说明：原始粘贴内容中包含导航栏、广告、页脚、侧栏、重复目录等噪声；部分图文结构在复制过程中被破坏。本文已去除页面噪声，并将原文中的卡片、图示、表格关系重组为结构化笔记。本文为学习整理，不是逐字转载。

---

## 1. 一句话理解

Harness Engineering 可以理解为：

```text
围绕 AI Agent 构建约束、反馈、控制、验证和持续改进系统的工程实践。
```

它不直接优化模型参数，也不只是优化 prompt，而是优化 Agent 运行的外部环境。

核心思想：

```text
人类掌舵，智能体执行。
Human Steer, Agent Execute.
```

也就是说，Agent 可以拥有更强的执行能力，但它必须在一套可控的系统中运行：有上下文入口、有架构边界、有自动反馈、有错误回流、有成本和质量控制。

---

## 2. Harness 这个词为什么合适

Harness 原意接近“马具、缰绳、马鞍、嚼子”。

这个比喻的重点不是削弱马的能力，而是让强大但不完全可预测的力量变得可驾驭。

对应到 AI Agent：

```text
模型能力越强，越需要更清晰的运行边界和反馈机制。
```

所以 Harness Engineering 的目标不是让 Agent 更保守，而是让 Agent 能在护栏内更稳定、更长期、更可复现地工作。

---

## 3. 与 Prompt Engineering / Context Engineering 的区别

| 范式 | 优化对象 | 解决的问题 | 交互模式 |
|---|---|---|---|
| Prompt Engineering | 输入措辞、格式、示例 | 单次回答质量 | 一问一答 |
| Context Engineering | 文档、代码片段、历史信息 | 模型该看什么，如何减少幻觉 | 信息注入后生成 |
| Harness Engineering | 约束、反馈回路、控制系统、运行环境 | Agent 如何可靠、持续、可控地工作 | 人类掌舵，Agent 执行 |

一个类比：

```text
Prompt Engineering：对马喊话的技巧。
Context Engineering：给马看的地图。
Harness Engineering：给马配缰绳、护栏、道路、限速牌和补给站。
```

因此，Harness Engineering 比 prompt 和 context 更靠近“工程系统设计”。

---

## 4. 为什么需要 Harness Engineering

随着 Agent 能完成更长链条的任务，主要问题不再只是“它会不会写代码”，而是：

- 会不会一次性做太多，导致上下文耗尽；
- 会不会过早宣布完成；
- 会不会只跑局部测试就认为功能可用；
- 会不会复制并放大代码库中的坏模式；
- 会不会制造技术债务和文档漂移；
- 会不会在失败后重复犯同样的错。

原文强调了一个关键判断：

```text
Agent 的每一次失败，都应该被看作运行环境设计不完善的信号。
```

正确的反应不是单纯换更强模型，而是把失败样例沉淀成新的约束、测试、文档、linter、CI 或流程规则。

---

## 5. Agent 的典型失败模式

### 5.1 试图一步到位

Agent 倾向于在一个会话中完成过多功能，结果上下文耗尽，留下半成品代码、缺失文档和难以接续的状态。

应对方式：

- 将任务切成小阶段；
- 将阶段状态持久化；
- 每阶段必须有明确完成条件；
- 不允许没有验证就进入下一阶段。

### 5.2 过早宣布胜利

Agent 看到项目中已经有部分成果，就可能误判为任务完成，即使还有大量工作未实现。

应对方式：

- 使用 `TASK_STATE.md` 或类似状态文件；
- 明确 active stage；
- 写清楚“当前不能做什么”；
- 用 harness 作为阶段完成依据。

### 5.3 过早标记功能完成

代码能跑、单元测试通过或 curl 返回成功，并不代表功能真正可用。

应对方式：

- 增加端到端验证；
- 增加 golden cases；
- 检查证据链；
- 让失败样例回流到 harness。

### 5.4 坏模式复制与架构漂移

Agent 很擅长模仿已有模式。如果代码库中有坏模式，它也会复制并放大。

应对方式：

- 架构约束自动化；
- 自定义 linter；
- CI 阻断；
- 文档持续更新；
- 定期清理技术债。

---

## 6. Harness 的四大护栏

原文将 Harness 的核心组件归纳为四类护栏：

```text
上下文工程 Context Engineering
架构约束 Architecture Constraints
反馈循环 Feedback Loop
熵管理 Entropy Management
```

### 6.1 上下文工程：给 Agent 的新员工手册

Agent 进入项目时需要知道：

- 当前项目做到哪里；
- 应该先读哪些文件；
- 当前阶段任务是什么；
- 哪些目录不能修改；
- 什么情况下需要停止；
- 失败后应该如何记录。

但上下文不是越多越好。过长的规则文件会挤占任务空间，也容易变成过时规则的堆积。

更好的方式：

```text
稳定、小巧的入口文件 + 按需检索更多上下文。
```

在我们的项目中，对应：

```text
TASK_STATE.md 作为真实项目入口。
Agent开发工程师.md 作为求职包装入口。
阶段文件按 TASK_STATE.md 指定读取。
```

### 6.2 架构约束：把规则写进系统

靠人提醒 Agent 不要越界是不可靠的。真正有效的是把架构规则编码为：

- linter；
- schema；
- CI；
- 类型系统；
- 目录边界；
- 写入权限限制。

一个重要细节：错误信息本身也是上下文工程。

好的错误信息不只是说“错了”，还应该告诉 Agent：

```text
违反了什么规则；
为什么有这个规则；
正确做法是什么；
下一步应该怎么修。
```

### 6.3 反馈循环：让系统知道自己错在哪里

反馈循环的核心是：

```text
运行 → 检查 → 失败 → 返回错误信息 → 修正 → 再运行。
```

在 Agent 项目中，反馈可以来自：

- 自动测试；
- harness case；
- schema validation；
- linter；
- CI；
- human review；
- agent-to-agent review。

重点不是“测试存在”，而是测试失败后能否把错误信息有效回传给执行者。

### 6.4 熵管理：持续小额偿还技术债

Agent 长期运行会带来：

- 文档过时；
- 规则漂移；
- bad case 堆积；
- 目录混乱；
- prompt 膨胀；
- 架构边界模糊。

因此需要定期做“小额垃圾回收”：

- 清理过时文档；
- 合并重复规则；
- 更新状态文件；
- 将失败样例加入 harness；
- 修复 schema 和代码之间的不一致。

这也是为什么 harness 不能只做一次，而要随着项目演进持续维护。

---

## 7. 六个行业共识

根据原文整理，Harness Engineering 背后的行业共识包括：

1. **瓶颈在基础设施，不只在模型智能**  
   很多 Agent 失败不是因为模型完全不会，而是运行环境没有足够约束和反馈。

2. **文档必须是活的反馈循环**  
   文档不是一次性说明书，而应该吸收失败案例、规则变化和阶段状态。

3. **思考与执行需要分离**  
   复杂任务不能全部塞进单个上下文窗口，需要规划层、执行层、状态持久化。

4. **上下文不是越多越好**  
   上下文是稀缺资源，应按需检索，而不是把所有规则一次性塞给 Agent。

5. **约束必须自动化**  
   不能依赖人工反复提醒。能写进 schema、linter、CI、harness 的规则都应该自动化。

6. **工程师角色在变化**  
   工程师不只是写代码，而是在设计能让 Agent 稳定工作的环境。

---

## 8. Harness 与 Agent 框架的关系

Harness 不是 LangGraph、AutoGen、CrewAI 这类 Agent 框架的替代品。

它更像是位于框架之上的“驾驭层”：

```text
Harness Layer
  - 约束
  - 反馈循环
  - 上下文工程
  - 熵管理
  - 生命周期管理

Agent Framework Layer
  - Agent 定义
  - 消息路由
  - 任务生命周期
  - 多智能体协作

SDK / API Layer
  - 模型调用
  - 工具注册
  - 流式输出

Foundation Model Layer
  - GPT / Claude / Gemini / DeepSeek 等模型能力
```

Agent 框架回答：

```text
如何构建智能体？
```

Harness 回答：

```text
如何让智能体可靠运行？
```

---

## 9. Harness 与测试、评估、Benchmark 的关系

Harness 可以包含测试和评估，但它比单纯测试更大。

| 名称 | 关注点 |
|---|---|
| Unit Test | 单个函数是否正确 |
| Integration Test | 多个模块能否一起跑 |
| Schema Validation | 输出结构是否合法 |
| Eval / Benchmark | 模型或系统在标准任务上的表现 |
| Harness | 任务运行环境、约束、反馈、评估、错误回流和持续改进 |

所以 harness 的核心不是“跑测试”，而是：

```text
把任务完成标准、运行边界、反馈机制和改进循环工程化。
```

---

## 10. Harness 如何判断对错

Harness 本身不凭空知道正确答案，它依赖外部判据。

常见来源：

### 10.1 人工标注 golden cases

人工确认一小批样本的标准答案。

例如：

```json
{"case_id":"version_001","doc_id":"DHF_xxx","task":"version","expected":{"value":"V1.0"}}
```

### 10.2 结构性规则

不需要语义理解即可判断。

例如：

```text
JSON 必须符合 schema。
每个 semantic_unit 必须有 evidence。
每个 evidence 必须能回到 source_anchor。
```

### 10.3 证据反查

系统抽出的结论必须能在原文中找到证据。

例如：

```text
抽出 version = V1.0，则 evidence text 中必须出现 V1.0 或对应版本表达。
```

### 10.4 bad case 回流

人工发现错误后，将错误样例加入 harness，避免下一轮重复犯错。

---

## 11. 映射到 DocReview 项目

当前 DocReview 项目非常适合作为 Harness Engineering 的练习项目，因为它天然需要：

- 多阶段处理；
- 结构化数据；
- 语义抽取；
- evidence anchor；
- 跨文档一致性；
- 分级路由；
- 成本控制；
- 人工复核。

阶段映射：

| 阶段 | 主要任务 | Harness 关注点 | Agent 是否必要 |
|---|---|---|---|
| T03 | normalized JSON | schema、重复、source_anchor、字段稳定性 | 不必要 |
| T04-1 | 语义抽取 | 抽取准确率、证据覆盖、schema 合法性 | 不必要，先规则 |
| T05 | knowledge index | 查询是否返回正确实体和证据 | 不必要 |
| T06 | 单文件一致性 | finding 是否准确、证据是否充分 | 复杂 case 可用 LLM |
| T07 | 跨文档一致性 | 冲突、断链、漂移是否识别正确 | 复杂推理可能需要 Agent/LLM |
| T08 | finding schema | 字段完整、证据链可追溯 | 不必要 |
| T09/T10 | pipeline + routing | 路由、成本、端到端输出 | 需要 Agent/routing |

核心策略：

```text
每阶段都要有 harness。
不是每阶段都要有 Agent。
先规则，后 LLM；先工具，后 Agent；先 harness，后扩量。
```

---

## 12. 当前 T04-1 的具体 harness 设计

T04-1 不应该先追求全量抽取，而应该先建立一个小型 `semantic_harness`。

### 12.1 评估对象

- document_role；
- version；
- parameter 子集；
- table_field 子集；
- evidence anchor。

### 12.2 输入

```text
outputs/t03_hybrid_auto/normalized_full/
```

### 12.3 case 文件

```text
harness/cases/semantic_public.jsonl
```

### 12.4 报告文件

```text
harness/reports/semantic_sample_report.json
```

### 12.5 最小指标

```text
schema_valid_rate = 1.0
evidence_coverage = 1.0
document_role_accuracy >= 0.8
version_accuracy >= 0.8
parameter_precision 初期优先于 recall
```

### 12.6 迭代方式

```text
人工标注 20-50 个 golden cases
  ↓
规则抽取器跑样本
  ↓
semantic_harness 评分
  ↓
记录 failed cases
  ↓
修正规则或 schema
  ↓
再次运行 harness
```

---

## 13. 学习 AI Agent 时的启发

如果只学习 prompt、tool calling、multi-agent workflow，很容易停留在“能让 Agent 动起来”的层面。

Harness Engineering 强调的是另一层能力：

- 任务定义；
- 上下文入口；
- 工具权限；
- 自动校验；
- 运行日志；
- golden cases；
- 错误归因；
- 成本统计；
- 人工复核；
- 状态持久化；
- 文档持续更新。

这正是从“会用 Agent”到“能工程化驾驭 Agent”的分水岭。

---

## 14. 最终总结

Harness Engineering 的核心不是给 Agent 更多自由，而是在更高自主性下提供更严格的运行约束。

一句话总结：

```text
Harness 是让 Agent 可靠工作的工程化环境：它通过上下文、约束、反馈、验证和熵管理，把 Agent 的失败转化为系统的持续改进。
```

对于 DocReview 项目，当前最重要的落点是：

```text
T04-1 先建立 semantic_harness，再围绕 harness 迭代规则抽取器。
```
