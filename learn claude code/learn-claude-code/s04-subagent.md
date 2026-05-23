# s04 Subagent 学习总结

## 本章主题

第四章学习的是 Subagent，也就是让父 Agent 把局部子任务交给一个拥有独立上下文的小 Agent 来完成。

前三章已经建立了基础能力：

```text
s01：Agent Loop，让模型能通过工具循环行动
s02：Tool Use，让模型能调用多个专用工具
s03：TodoWrite，让模型能追踪多步任务进度
```

但复杂任务继续执行时，会出现新的问题：

```text
主 Agent 的 messages 会被大量文件内容、命令输出、搜索结果和中间分析污染。
```

s04 的核心目标就是：

```text
把局部探索交给 Subagent，在独立 messages[] 中完成，只把最终结果返回给父 Agent。
```

本章核心格言是：

```text
大任务拆小，每个小任务干净的上下文。
```

---

## 一、为什么需要 Subagent

主 Agent 的上下文非常宝贵。

当 Agent 执行复杂任务时，会不断产生：

- 文件内容；
- 命令输出；
- 搜索结果；
- 错误日志；
- 中间分析；
- 工具返回值。

这些都会进入 `messages`。

但很多信息只是为了完成某个局部任务，父 Agent 最终并不需要完整保留。

例如用户问：

```text
这个项目用什么测试框架？
```

Subagent 可能需要读：

```text
requirements.txt
pyproject.toml
README.md
tests/ 目录
```

但父 Agent 最终只需要知道：

```text
这个项目使用 pytest。
```

因此 Subagent 的作用是：

```text
让局部探索在独立上下文中完成，父 Agent 只接收最终结果。
```

---

## 二、Subagent 和普通工具调用有什么区别

普通工具调用通常是一次动作：

```text
read_file("main.py")
bash("pytest")
write_file("hello.py", content)
```

特点是：

- 一次调用；
- 一次执行；
- 一次返回；
- 通常比较确定。

Subagent 则是启动一个新的 Agent Loop。

它可以自己多轮行动：

```text
读文件
搜索目录
执行命令
分析结果
再次读文件
最后总结
```

所以二者区别是：

| 类型 | 普通工具调用 | Subagent |
|---|---|---|
| 本质 | 一个函数 / 工具 | 一个小 Agent |
| 上下文 | 使用父 Agent 上下文 | 使用独立 messages |
| 执行步数 | 通常一步 | 可以多步 |
| 适合任务 | 明确动作 | 探索性子任务 |
| 返回结果 | 工具结果 | 子任务总结 |

一句话总结：

```text
普通工具是一个动作，Subagent 是一个独立完成子任务的小循环。
```

---

## 三、为什么 Subagent 可以减少上下文污染

Subagent 有自己的 `messages[]`。

父 Agent 的上下文是：

```text
parent messages
```

Subagent 的上下文是：

```text
sub_messages
```

Subagent 运行时，所有中间过程都会进入 `sub_messages`：

- 读了哪些文件；
- 跑了哪些命令；
- 看到了哪些日志；
- 做了哪些中间判断。

但任务结束后，`sub_messages` 会被丢弃。

父 Agent 只收到最终摘要，例如：

```text
项目使用 pytest，测试文件在 tests/ 目录。
```

这样主上下文不会塞进大量临时信息。

所以 Subagent 的上下文隔离效果是：

```text
脏活累活在子上下文里完成，主上下文只保留结论。
```

---

## 四、父 Agent 和 Subagent 共享什么、不共享什么

父 Agent 和 Subagent 共享：

- 工作目录；
- 文件系统；
- 基础工具；
- 模型能力；
- 部分运行环境。

例如 Subagent 可以和父 Agent 一样读取项目文件、执行命令、修改文件。

它们不共享：

- 完整 messages 历史；
- 父 Agent 的长期上下文；
- Subagent 的中间工具调用过程；
- Subagent 的临时分析轨迹。

可以总结为：

```text
它们共享环境，不共享记忆。
```

这个设计让 Subagent 能完成实际任务，但不会把大量中间过程带回父 Agent。

---

## 五、为什么 Subagent 不应该拥有 task 工具

如果 Subagent 也能调用 `task`，就可能递归创建更多 Subagent：

```text
父 Agent 创建 Subagent
Subagent 再创建 Subagent
子 Subagent 再创建子 Subagent
...
```

这会带来风险：

- 无限递归；
- 成本失控；
- 上下文难以追踪；
- 任务责任不清；
- 调试困难；
- 执行时间不可控。

所以 s04 的设计通常是：

```text
父 Agent 有 task 工具。
Subagent 没有 task 工具。
```

这样可以保证：

```text
只有父 Agent 能分派子任务，Subagent 只负责完成当前子任务。
```

这是一个简单但重要的安全边界。

---

## 六、Subagent 最后应该返回什么

教学版本里，Subagent 最后返回一段摘要文本。

例如：

```text
项目使用 pytest，测试文件位于 tests/ 目录。
```

但在真实工程里，只返回自然语言摘要通常不够。

更好的返回是结构化结果：

```json
{
  "status": "completed",
  "summary": "项目使用 pytest，测试文件位于 tests/ 目录。",
  "evidence": [
    {
      "source": "requirements.txt",
      "text": "pytest"
    }
  ],
  "files_read": [
    "requirements.txt",
    "pyproject.toml"
  ],
  "confidence": "high",
  "errors": []
}
```

这样父 Agent 既能保持上下文干净，又能保留必要证据。

总结：

```text
教学版：返回最终摘要文本。
工程版：返回结构化摘要 + 证据 + 状态 + 错误信息。
```

---

## 七、企业流程 Agent 中，哪些任务适合交给 Subagent

适合交给 Subagent 的任务通常有这些特征：

- 需要读很多资料；
- 需要多步探索；
- 中间过程很长；
- 最终只需要一个结论或结构化结果；
- 可以独立完成；
- 不强依赖主上下文的全部细节。

在企业文档审查 Agent 中，适合拆给 Subagent 的任务包括：

```text
文档解析 Subagent：解析 PDF / Word / Excel，输出结构化文本和 metadata。

语义抽取 Subagent：抽取实体、时间、指标、承诺、条件。

一致性检查 Subagent：检查同一文档内部是否自相矛盾。

跨文档对比 Subagent：比较多个文档中的字段、参数、日期是否一致。

引用校验 Subagent：检查报告中的引用是否真实存在。

风险分级 Subagent：判断问题严重程度。

测试 / 评估 Subagent：运行评测集并总结失败样例。
```

不适合交给 Subagent 的任务包括：

- 需要父 Agent 直接做最终决策的任务；
- 需要完整业务上下文的任务；
- 高风险不可自动执行的操作；
- 非常简单的一步工具调用。

核心判断标准是：

```text
如果一个子任务会产生大量中间上下文，但父 Agent 最终只需要结果，就适合用 Subagent。
```

---

## 八、s04 与 s03 的关系

s03 TodoWrite 解决的是：

```text
一个 Agent 如何管理多步任务进度。
```

s04 Subagent 解决的是：

```text
一个 Agent 如何把局部子任务委托出去，避免主上下文污染。
```

两者可以组合：

```text
父 Agent 用 TodoWrite 管理整体计划。
遇到探索性任务时，用 task 启动 Subagent。
Subagent 完成探索后返回摘要。
父 Agent 根据摘要更新 todo。
```

例如：

```text
[>] #2: 分析项目测试框架
```

父 Agent 可以调用：

```text
task(prompt="Find what testing framework this project uses")
```

Subagent 返回：

```text
This project uses pytest.
```

父 Agent 再更新 todo：

```text
[x] #2: 分析项目测试框架
```

---

## 九、Subagent 的风险

Subagent 不是越多越好。

它也有风险：

- 成本增加：每个 Subagent 都会调用模型；
- 延迟增加：子任务需要单独执行；
- 摘要损失：子 Agent 返回摘要时可能丢掉细节；
- 责任不清：如果子任务描述不清，结果可能偏离；
- 结果不可审计：如果只保留摘要，中间证据可能丢失。

因此在实际工程中，Subagent 的返回最好是结构化结果，而不只是自然语言摘要。

---

## 十、本章核心认知

本章最重要的认知是：

```text
Subagent 的价值不是“多一个模型”，而是“给局部任务一个干净上下文，并让主上下文只保留结果”。
```

可以进一步总结为：

```text
父 Agent 管全局。
Subagent 做局部。
中间过程隔离。
最终结果汇总。
```

---

## 十一、本章需要记住的问题答案

### 1. 为什么需要 Subagent？

因为复杂任务会让父 Agent 的 messages 被大量中间信息污染。Subagent 可以在独立上下文中完成局部探索，只把最终结果返回给父 Agent。

### 2. Subagent 和普通工具调用有什么区别？

普通工具调用是一次动作；Subagent 是一个拥有独立 messages 的小 Agent Loop，可以多轮调用工具并完成探索性子任务。

### 3. 为什么 Subagent 可以减少上下文污染？

因为 Subagent 的中间过程保存在自己的 `sub_messages` 中，任务完成后被丢弃，父 Agent 只收到最终摘要或结构化结果。

### 4. 父 Agent 和 Subagent 共享什么、不共享什么？

共享工作目录、文件系统、基础工具和运行环境；不共享完整 messages 历史、中间工具调用过程和临时分析轨迹。

### 5. 为什么 Subagent 不应该拥有 task 工具？

为了防止递归创建子 Agent，避免无限套娃、成本失控、任务责任不清和调试困难。

### 6. Subagent 最后应该返回什么？

教学版返回摘要文本；工程版应该返回结构化摘要、证据、状态、读取文件、置信度和错误信息。

### 7. 企业流程 Agent 中，哪些任务适合交给 Subagent？

适合那些需要多步探索、产生大量中间上下文、但最终只需要结论或结构化结果的任务，例如文档解析、语义抽取、一致性检查、跨文档对比、引用校验、风险分级和评估任务。

---

## 十二、下一步学习目标

下一章 s05 Skills 将解决：

```text
Agent 需要领域知识时，如何按需加载，而不是把所有知识都塞进 system prompt。
```

重点关注：

- Skill 是什么；
- 为什么知识不能全部放进 system prompt；
- Skill 如何通过工具结果注入；
- Skill 和 Subagent 的区别；
- 企业流程中如何设计领域 Skill。
