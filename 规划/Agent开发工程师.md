# Agent 开发工程师一个月学习与投递计划

## 目标

在 1 个月内具备投递 AI Agent 工程师 / 大模型应用开发工程师 / RAG & Agent 工程实习岗位的基本竞争力，完成至少 1 个可展示核心项目、1 个补充 Demo、1 份简历、1 套面试表达材料。

---

## 岗位定位

目标岗位不是基础大模型训练岗，而是：

- AI Agent 开发工程师；
- 大模型应用开发工程师；
- RAG / Agent 工程师；
- LLMOps / AI 应用平台开发实习生；
- 企业智能体应用开发实习生。

核心能力是：

> 能把大模型、RAG、工具调用、工作流、后端服务和评测体系组合成一个可运行、可展示、可解释、可迭代的 AI Agent 应用。

---

## 一个月最终交付物

### 必须完成

1. 一个主项目：Agent + RAG + Tool Calling 的完整项目；
2. 一个补充项目：LLMOps 评测 / Code Review Agent / 文档一致性 Agent 三选一；
3. GitHub README；
4. 项目架构图；
5. 简历项目描述；
6. 面试讲解稿；
7. 20 个高频面试题准备。

### 推荐主项目名称

> 企业文档审查 Agent：基于 RAG、工具调用和评测 Harness 的多文档一致性分析系统

---

## 技术栈选择

### 必选

- Python；
- FastAPI；
- LangGraph；
- LangChain 或 LlamaIndex；
- OpenAI-compatible API / Qwen API；
- Faiss 或 Chroma；
- Docker；
- GitHub README。

### 重要加分

- Rerank；
- MCP 基础；
- SQL / SQLite；
- LLMOps 评测 Harness；
- 日志 Trace；
- 成本统计；
- Streamlit 前端。

---

## 第 1 周：补齐 Agent 与 RAG 基础，完成最小 Demo

### 目标

完成一个最小可运行 Agent：支持用户提问、检索文档、调用工具、返回结构化结果。

### 学习内容

- LLM API 调用；
- Prompt Engineering；
- Function Calling / Tool Calling；
- RAG 基本流程；
- LangGraph 基础；
- FastAPI 基础。

### 任务

1. 完成 LLM API 调用封装；
2. 完成文档加载与切分；
3. 完成 Embedding 与向量检索；
4. 完成一个基础 RAG 问答接口；
5. 完成一个简单工具调用，例如文件读取、JSON 解析或关键词统计；
6. 用 LangGraph 串联：输入问题 → 检索 → 工具调用 → 生成答案。

### 第 1 周交付物

- `rag_demo.py`；
- `agent_graph.py`；
- `FastAPI /query` 接口；
- 3 条测试样例；
- README 初稿。

---

## 第 2 周：完成主项目核心功能

### 目标

将 Demo 升级为一个面向真实场景的 Agent 项目。

### 推荐场景

复杂文档审查 / 多文档一致性检查。

### 核心模块

1. 文档上传与解析；
2. 文档切分与向量索引；
3. RAG 检索；
4. Agent 工具调用；
5. 多步骤工作流；
6. 结果结构化输出；
7. 引用溯源；
8. 错误处理。

### Agent 工具建议

- `read_file_tool`：读取文件内容；
- `search_doc_tool`：检索相关片段；
- `compare_sections_tool`：比较两个文档片段；
- `extract_claims_tool`：抽取关键事实；
- `generate_report_tool`：生成审查报告；
- `cost_counter_tool`：统计 Token 或调用次数。

### 第 2 周交付物

- 可运行主项目；
- 至少 5 个工具；
- 至少 1 条完整 Agent Workflow；
- 示例输入文档；
- 示例输出报告；
- README 中写清楚使用方式。

---

## 第 3 周：补齐工程化、评测和展示能力

### 目标

让项目从“能跑”变成“像真实工程项目”。

### 学习内容

- LLMOps 基础；
- 自动化评测；
- 日志记录；
- 成本统计；
- Docker；
- 项目结构整理。

### 任务

1. 增加评测集，例如 10 个问题；
2. 增加评测脚本，输出准确率、引用命中率、失败原因；
3. 增加日志：记录输入、检索片段、工具调用、最终回答；
4. 增加成本统计：Token、调用次数、耗时；
5. 增加 Dockerfile；
6. 增加 Streamlit 或简单前端；
7. 画架构图。

### 第 3 周交付物

- `eval.py`；
- `logs/` 示例；
- `Dockerfile`；
- 架构图；
- README 完整版；
- 项目 Demo 截图。

---

## 第 4 周：简历、面试和投递准备

### 目标

把项目转化为可投递材料。

### 任务

1. 完成简历项目描述；
2. 准备项目讲解稿；
3. 准备常见面试题；
4. 修改 GitHub README；
5. 准备投递岗位关键词；
6. 每天投递并根据 JD 微调简历。

### 简历项目描述模板

- 基于 FastAPI、LangGraph 和向量数据库构建企业文档审查 Agent，支持文档解析、RAG 检索、工具调用、多步骤工作流和结构化审查报告生成。
- 设计并实现文件读取、文档检索、关键信息抽取、片段对比、报告生成等工具，支持 Agent 根据任务自动编排调用链路。
- 构建评测 Harness，记录检索命中率、引用正确率、工具调用成功率、响应耗时和调用成本，用于持续优化 Agent 效果。
- 使用 Docker 封装服务，并提供 FastAPI 接口和示例数据，支持一键启动与结果复现。

---

## 每周时间分配

### 每天 3 小时版本

- 1 小时：学习核心概念；
- 1.5 小时：写项目代码；
- 0.5 小时：整理 README / 笔记 / 面试表达。

### 每天 5 小时版本

- 1 小时：学习；
- 3 小时：项目开发；
- 1 小时：工程化、文档、复盘。

---

## 需要重点掌握的面试问题

### Agent 基础

- Agent 和 Chatbot 的区别是什么？
- Agent 通常由哪些模块组成？
- Tool Calling 是如何工作的？
- LangGraph 和普通链式调用有什么区别？
- 如何避免 Agent 无限循环？
- 工具调用失败怎么办？

### RAG

- RAG 的完整流程是什么？
- Chunk 大小如何选择？
- Top-K 如何设置？
- 为什么需要 Rerank？
- 如何评估 RAG 效果？
- Graph RAG 和普通 RAG 的区别是什么？

### 工程化

- 如何用 FastAPI 封装 Agent 服务？
- 如何记录 Agent 执行日志？
- 如何统计 Token 成本？
- 如何设计 Agent 的错误处理？
- Docker 在部署中解决什么问题？

### 项目讲解

- 你的项目解决了什么问题？
- 为什么适合用 Agent？
- 你的工具链如何设计？
- 你的 RAG 如何做检索和引用？
- 你的评测体系如何设计？
- 项目有哪些不足和下一步优化？

---

## 投递关键词

简历和项目中应高频出现：

- AI Agent；
- RAG；
- Tool Calling；
- Function Calling；
- LangGraph；
- LangChain；
- FastAPI；
- 向量数据库；
- Embedding；
- Rerank；
- LLMOps；
- Agent Workflow；
- 多步骤任务执行；
- Docker；
- Prompt Engineering；
- 文档智能；
- 结构化输出；
- 评测 Harness。

---

## 一个月内不建议深挖的内容

为了投递效率，暂时不要把主要时间投入：

- 从零训练大模型；
- 深入 RLHF / PPO / GRPO；
- 复杂 Kubernetes；
- 大规模分布式训练；
- 过深的 C++ 推理优化；
- 太复杂的前端。

这些可以作为了解项，不要影响主项目交付。

---

## 最终目标状态

一个月后应能做到：

1. 能讲清 Agent、RAG、Tool Calling、LangGraph 的基本原理；
2. 能展示一个完整 AI Agent 项目；
3. 能展示 GitHub README、架构图和 Demo 截图；
4. 能解释项目中的工具链、工作流、评测和工程化设计；
5. 能写出匹配 AI Agent 工程师岗位的简历项目经历；
6. 能开始投递 AI Agent 工程师、大模型应用开发、RAG 工程、LLMOps 实习岗位。
