# s02 Tool Use 学习总结

## 本章主题

第二章学习的是 Tool Use，也就是如何让 Agent 从“只能通过 Bash 行动”，扩展为“可以调用多个专用工具行动”。

本章的核心格言是：

```text
加一个工具，只加一个 handler。
```

也就是说，Agent Loop 不需要因为新增工具而改变。新增工具时，只需要：

1. 定义工具 schema；
2. 实现工具 handler；
3. 把工具注册进 dispatch map。

Agent Loop 仍然保持第一章的结构：

```text
用户输入
→ LLM 判断是否调用工具
→ Harness 根据工具名找到 handler
→ 执行工具
→ 工具结果回填到 messages
→ LLM 继续判断
```

---

## 一、为什么不能所有操作都只靠 Bash

Bash 是一个非常通用的工具，但它不适合承担所有动作。

只用 Bash 会有几个问题：

- 不够结构化；
- 安全面太大；
- 文件读写不稳定；
- 权限控制困难；
- 错误结果不一定适合模型继续推理。

例如读文件可以用：

```bash
cat main.py
```

但 `cat` 输出可能太长，不容易控制读取范围。

写文件可以用：

```bash
echo "..." > file.py
```

但多行文本、引号、转义字符、特殊符号都可能导致失败。

修改文件可以用：

```bash
sed -i 's/old/new/g' file.py
```

但 `sed` 遇到特殊字符、换行、引号时很容易出错。

因此，本章引入了专用工具：

```text
read_file
write_file
edit_file
```

这些工具比 Bash 更清晰、更稳定、更容易加权限控制。

---

## 二、schema 和 handler 分别是什么

一个工具至少由两部分组成：

```text
schema：给模型看的工具说明书
handler：Harness 真正执行工具的函数
```

schema 告诉模型：

- 工具叫什么；
- 工具能做什么；
- 输入参数有哪些；
- 哪些参数是必填；
- 参数类型是什么。

handler 则是真正执行动作的代码。

例如 `read_file` 的 schema 可以告诉模型：

```text
工具名：read_file
作用：读取文件内容
必填参数：path
可选参数：limit
```

而 handler 则负责：

```text
检查路径是否合法
读取文件内容
限制返回长度
捕获错误
返回结果
```

所以要记住：

```text
schema 是给模型看的。
handler 是 Harness 执行的。
```

---

## 三、dispatch map 解决什么问题

dispatch map 解决的是：模型请求调用某个工具后，Harness 如何找到对应的执行函数。

它本质上是一个映射表：

```python
TOOL_HANDLERS = {
    "bash":       lambda **kw: run_bash(kw["command"]),
    "read_file":  lambda **kw: run_read(kw["path"], kw.get("limit")),
    "write_file": lambda **kw: run_write(kw["path"], kw["content"]),
    "edit_file":  lambda **kw: run_edit(kw["path"], kw["old_text"], kw["new_text"]),
}
```

执行流程是：

```text
模型请求调用 read_file
→ Harness 拿到工具名 read_file
→ 在 TOOL_HANDLERS 中查找
→ 找到 run_read
→ 把模型传入的参数交给 run_read
→ 执行工具
→ 把结果回填给模型
```

dispatch map 的优势是避免在主循环里写大量 `if / elif`。

更重要的是：

```text
新增工具时，不需要修改 Agent Loop。
```

---

## 四、新增一个工具需要改哪些地方

新增一个工具通常需要改 3 个地方：

1. 写 handler 函数；
2. 写工具 schema；
3. 注册到 dispatch map。

例如新增 `list_dir` 工具：

```python
def run_list_dir(path: str = ".") -> str:
    fp = safe_path(path)
    return "\n".join(p.name for p in fp.iterdir())
```

然后写 schema：

```python
{
    "name": "list_dir",
    "description": "List files in a directory.",
    "input_schema": {
        "type": "object",
        "properties": {
            "path": {"type": "string"}
        },
        "required": []
    }
}
```

最后注册到 dispatch map：

```python
TOOL_HANDLERS = {
    "list_dir": lambda **kw: run_list_dir(kw.get("path", "."))
}
```

主循环不需要变。

这是本章最重要的工程原则。

---

## 五、safe_path 的作用

`safe_path` 的作用是防止 Agent 访问工作区外的文件。

例如当前工作区是：

```text
/project/agent-demo
```

模型可能尝试读取：

```text
../../.env
```

如果没有路径安全检查，Agent 可能读到敏感文件。

`safe_path` 会把路径解析成真实绝对路径，然后检查它是否仍然位于工作区内。

如果路径逃逸，就拒绝执行。

这体现了 Harness 的权限边界：

```text
模型可以决定要读文件，但 Harness 决定它是否被允许真的读取。
```

所以模型负责决策，不代表模型拥有无限权限。

---

## 六、为什么说工具定义了 Agent 的能力边界

模型本身只能生成文本。

它不能真的读文件、写文件、查数据库、发邮件、创建工单。

它能行动，是因为 Harness 给了它工具。

给它什么工具，它就能触达什么环境：

```text
给 read_file，它能读文件
给 write_file，它能写文件
给 bash，它能执行命令
给 search_doc，它能检索文档
给 query_db，它能查询数据库
给 send_email，它能发送邮件
给 create_ticket，它能创建工单
```

因此，Agent 的能力边界由工具决定。

可以总结为：

```text
模型决定怎么做。
工具决定能做什么。
权限决定允许做到哪一步。
```

工具越清晰、越稳定、越安全，Agent 越可靠。

---

## 七、企业流程 Agent 中如何设计工具

企业流程 Agent 不应该直接把底层系统暴露给模型。

例如，不建议一开始就给模型一个过于自由的工具：

```text
execute_sql(sql)
```

更好的方式是封装成业务动作：

```text
query_customer_profile(customer_id)
get_order_status(order_id)
search_policy_documents(query)
create_approval_task(applicant, reason)
generate_review_report(findings)
```

设计原则：

- 工具要原子化；
- 工具名要清楚；
- 输入输出要结构化；
- 权限边界要明确；
- 错误信息要可读；
- 工具结果要方便模型继续推理。

以企业文档审查 Agent 为例，可以设计：

```text
read_document(file_id)
search_document(query, top_k)
extract_claims(chunk_id)
compare_claims(claim_a, claim_b)
check_citation(report_item)
generate_review_report(issues)
create_review_task(issue)
```

模型只调用这些受控工具。Harness 在背后连接真实的文件系统、向量数据库、OCR、PDF 解析器、审批系统和工单系统。

---

## 八、本章建立的核心认知

本章最重要的认知是：

```text
加工具不是改 Agent Loop，而是扩展 Harness 的行动接口。
```

Agent Loop 是稳定的：

```text
LLM 请求工具
Harness 执行工具
工具结果回填
LLM 继续判断
```

Tool Use 层负责扩展 Agent 能力：

```text
工具 schema 让模型知道有什么能力。
handler 让 Harness 真正执行能力。
dispatch map 让工具名映射到执行函数。
safe_path 和权限检查限制工具能做什么。
```

---

## 九、本章需要记住的问题答案

### 1. 为什么不能所有操作都只靠 Bash？

因为 Bash 虽然通用，但不够稳定、不够安全、不够结构化。专用工具可以让文件读写、编辑等常见动作更可控、更易限制权限。

### 2. schema 和 handler 分别是什么？

schema 是给模型看的工具说明书，描述工具名、作用和参数；handler 是 Harness 真正执行工具的函数。

### 3. dispatch map 解决了什么问题？

它把工具名映射到对应 handler，让 Harness 能根据模型请求的工具名找到执行函数，避免主循环写大量分支。

### 4. 新增一个工具需要改哪些地方？

需要新增 handler、定义工具 schema、注册到 dispatch map。Agent Loop 不需要改。

### 5. safe_path 的作用是什么？

防止模型通过相对路径或绝对路径访问工作区外的文件，是文件工具的路径沙箱。

### 6. 为什么说工具定义了 Agent 的能力边界？

因为模型本身只能生成文本，只有通过工具才能行动。给它什么工具，它就能做什么事。

### 7. 企业流程 Agent 中应该如何设计工具？

应该把底层系统封装成清晰、原子化、结构化、受权限控制的业务工具，而不是直接暴露数据库、文件系统或任意命令执行能力。

---

## 十、下一步学习目标

下一章 s03 TodoWrite 将解决：

```text
Agent 有了工具之后，如何避免走一步看一步？
```

重点关注：

- 为什么 Agent 需要任务清单；
- TodoWrite 如何帮助模型规划；
- 如何追踪任务状态；
- 为什么计划能力能提高复杂任务完成率；
- 企业流程 Agent 中如何把 TodoWrite 扩展为任务系统。
