# Agent 开发工程师任务清单

## 目标

目标是在一个月内完成一套可投递 AI Agent 工程师岗位的作品与材料。

最终需要具备：

- 一个能运行的 Agent 项目；
- 一个能展示的 GitHub 仓库；
- 一份能投递的简历；
- 一套能讲清楚项目的面试材料。

---

## 任务 1：确定主项目方向

主项目建议固定为：

> 企业文档审查 Agent：基于 RAG、工具调用和评测 Harness 的多文档一致性分析系统

你需要明确：

- 项目解决什么问题；
- 为什么这个问题适合用 Agent；
- 用户输入是什么；
- Agent 输出是什么；
- 需要调用哪些工具；
- 如何判断结果好坏。

交付物：

- 一段项目简介；
- 一张业务流程草图；
- 一张 Agent 执行流程草图。

---

## 任务 2：搭建项目基础结构

建议目录：

```text
agent-doc-review/
├── app/
│   ├── main.py
│   ├── config.py
│   ├── llm.py
│   ├── graph.py
│   ├── tools/
│   ├── rag/
│   ├── schemas/
│   └── utils/
├── data/
├── eval/
├── logs/
├── tests/
├── README.md
├── Dockerfile
└── requirements.txt
```

必须完成：

- 创建 GitHub 仓库；
- 建立 Python 项目结构；
- 配置 `.env.example`；
- 配置 `requirements.txt`；
- 配置基础 README。

---

## 任务 3：封装大模型调用

需要完成：

- 封装统一 LLM Client；
- 支持 OpenAI-compatible API 或 Qwen API；
- 支持传入 system prompt、user prompt；
- 支持结构化 JSON 输出；
- 记录调用耗时、Token 或调用次数；
- 做基础异常处理。

交付物：

- `app/llm.py`；
- 一个可运行的调用测试脚本；
- README 中说明如何配置 API Key。

---

## 任务 4：完成文档解析模块

需要完成：

- 支持读取 Markdown / TXT；
- 可选支持 PDF；
- 统一输出文档对象；
- 保留文件名、段落编号、页码或位置等 metadata；
- 对异常文件给出错误信息。

交付物：

- `app/rag/loader.py`；
- 3 个示例文档；
- 文档解析测试结果。

---

## 任务 5：完成 Chunk 切分模块

需要完成：

- 实现固定长度切分；
- 实现按标题或段落切分；
- 保留 chunk_id、source、position；
- 支持 overlap；
- 能解释为什么这样切分。

交付物：

- `app/rag/splitter.py`；
- 示例 chunk 输出；
- README 中说明切分策略。

---

## 任务 6：完成向量检索模块

需要完成：

- 接入 Embedding；
- 使用 Faiss 或 Chroma 建立向量索引；
- 支持 Top-K 检索；
- 返回 chunk 内容和 metadata；
- 支持按文件过滤；
- 支持重建索引。

交付物：

- `app/rag/vector_store.py`；
- `build_index.py`；
- `search_demo.py`。

---

## 任务 7：完成基础 RAG 问答

需要完成：

- 用户输入问题；
- 检索相关 chunk；
- 组装上下文；
- 调用 LLM 生成答案；
- 输出引用来源；
- 对无相关内容的问题给出兜底回答。

交付物：

- `app/rag/retriever.py`；
- `app/rag/qa.py`；
- 至少 5 个问答样例。

---

## 任务 8：设计 Agent 工具清单

至少实现 5 个工具：

1. `read_file_tool`：读取指定文件；
2. `search_doc_tool`：检索文档片段；
3. `extract_claims_tool`：抽取事实、指标、时间、实体；
4. `compare_sections_tool`：比较两个片段是否一致；
5. `generate_report_tool`：生成结构化审查报告。

加分工具：

- `cost_counter_tool`：统计调用成本；
- `risk_label_tool`：给问题打风险标签；
- `citation_check_tool`：检查引用是否存在；
- `json_validate_tool`：检查输出格式。

交付物：

- `app/tools/` 下的工具函数；
- 每个工具的输入输出 schema；
- 每个工具的独立测试。

---

## 任务 9：使用 LangGraph 编排 Agent Workflow

建议流程：

```text
用户问题
→ 意图识别
→ 文档检索
→ 信息抽取
→ 片段对比
→ 一致性判断
→ 报告生成
→ 引用检查
→ 最终输出
```

需要完成：

- 定义 State；
- 定义节点；
- 定义边；
- 支持条件分支；
- 支持失败重试；
- 支持最终结构化输出。

交付物：

- `app/graph.py`；
- 一张 Agent Workflow 图；
- 3 个完整运行案例。

---

## 任务 10：设计结构化输出格式

输出建议包含：

```json
{
  "summary": "结论摘要",
  "issues": [
    {
      "type": "不一致 / 缺失 / 风险 / 无问题",
      "severity": "high / medium / low",
      "description": "问题描述",
      "evidence": ["引用片段"],
      "suggestion": "修改建议"
    }
  ],
  "citations": [
    {
      "source": "文件名",
      "chunk_id": "chunk 编号"
    }
  ]
}
```

需要完成：

- Pydantic Schema；
- JSON 输出校验；
- 输出失败时自动修复或重试。

交付物：

- `app/schemas/report.py`；
- 示例输出报告。

---

## 任务 11：封装 FastAPI 服务

需要完成接口：

- `POST /upload`：上传文档；
- `POST /index`：建立索引；
- `POST /query`：普通 RAG 问答；
- `POST /agent/review`：Agent 审查；
- `GET /health`：健康检查；
- `GET /logs/{run_id}`：查看执行日志。

交付物：

- `app/main.py`；
- FastAPI Swagger 页面截图；
- curl 调用示例。

---

## 任务 12：增加执行日志和 Trace

每次 Agent 运行要记录：

- run_id；
- 用户输入；
- 检索到的 chunks；
- 调用过哪些工具；
- 每个工具输入输出；
- LLM 最终回答；
- 耗时；
- 错误信息。

交付物：

- `logs/run_xxx.json`；
- 日志查看接口；
- README 中展示一份日志样例。

---

## 任务 13：增加成本和性能统计

需要记录：

- 调用次数；
- Token 数或近似字符数；
- 单次请求耗时；
- 每个节点耗时；
- 检索耗时；
- 工具调用成功率；
- 失败原因。

交付物：

- `app/utils/metrics.py`；
- 一份成本统计样例；
- README 中解释如何分析成本。

---

## 任务 14：构建评测集

至少准备 10 条评测样例：

- 3 条普通事实问答；
- 3 条跨文档一致性问题；
- 2 条缺失信息问题；
- 1 条无关问题；
- 1 条格式约束问题。

每条样例包含：

- question；
- expected_answer；
- expected_sources；
- evaluation_type。

交付物：

- `eval/eval_set.jsonl`；
- 10 条评测数据。

---

## 任务 15：实现评测 Harness

至少评估：

- answer_correctness；
- citation_hit_rate；
- tool_success_rate；
- format_valid_rate；
- latency；
- failure_reason。

可以先用规则评估，不必一开始就接复杂评测框架。

交付物：

- `eval/eval.py`；
- `eval/report.md`；
- 一张评测结果表。

---

## 任务 16：增加 Docker 部署

需要完成：

- Dockerfile；
- docker build 成功；
- docker run 成功；
- README 中提供一键启动命令。

交付物：

- `Dockerfile`；
- `.dockerignore`；
- 启动截图或命令记录。

---

## 任务 17：整理 README

README 必须包含：

- 项目背景；
- 项目亮点；
- 架构图；
- 技术栈；
- 功能列表；
- 快速开始；
- API 文档；
- Agent Workflow；
- 工具清单；
- RAG 流程；
- 评测结果；
- 示例输入输出；
- 局限性与未来优化。

交付物：

- 完整 README；
- Demo 截图；
- 架构图。

---

## 任务 18：补充一个前端或交互 Demo

二选一：

- Streamlit 前端；
- 简单 HTML 页面。

功能：

- 上传文档；
- 输入问题；
- 查看 Agent 审查报告；
- 查看引用来源；
- 查看执行日志。

交付物：

- `frontend/` 或 `streamlit_app.py`；
- Demo 截图。

---

## 任务 19：准备简历项目描述

准备三个版本：

### 简短版

基于 FastAPI、LangGraph 和向量数据库构建企业文档审查 Agent，支持 RAG 检索、工具调用、多步骤工作流、结构化报告生成和评测 Harness。

### 标准版

基于 FastAPI、LangGraph、Chroma/Faiss 构建企业文档审查 Agent，支持文档解析、向量检索、Tool Calling、多步骤工作流、引用溯源和结构化审查报告生成；设计文件读取、文档检索、事实抽取、片段对比、报告生成等工具，并构建评测 Harness 统计检索命中率、引用正确率、工具调用成功率、响应耗时和调用成本。

### 面试展开版

准备一段 2 分钟讲解，说明：背景、问题、架构、Agent 流程、工具链、RAG 方案、评测体系、项目难点、未来优化。

---

## 任务 20：准备面试题答案

必须准备：

1. Agent 和 Chatbot 的区别是什么？
2. Agent 包含哪些核心模块？
3. Tool Calling 是如何工作的？
4. LangGraph 相比普通 Chain 有什么优势？
5. 如何避免 Agent 无限循环？
6. 工具调用失败怎么办？
7. RAG 的完整流程是什么？
8. Chunk 大小如何选择？
9. 为什么需要 Rerank？
10. 如何评估 RAG 效果？
11. 如何设计 Agent 的日志系统？
12. 如何统计 Token 成本？
13. 如何用 FastAPI 封装 Agent 服务？
14. Docker 解决什么问题？
15. 你的项目有哪些不足？

交付物：

- `面试题.md`；
- 每题不少于 100 字答案。

---

## 任务 21：补充一个加分 Demo

三选一：

### 选项 A：Code Review Agent

功能：读取 Git diff，分析风险，生成 Review 建议。

### 选项 B：SQL Agent

功能：自然语言转 SQL，查询 SQLite，生成分析报告。

### 选项 C：MCP Tool Demo

功能：写一个简单 MCP Server，将文件搜索或数据库查询封装为工具。

推荐优先级：

1. Code Review Agent；
2. SQL Agent；
3. MCP Tool Demo。

---

## 任务 22：准备投递材料

需要准备：

- 简历 PDF；
- GitHub 项目链接；
- 项目 README；
- 项目截图；
- 面试讲解稿；
- 针对不同 JD 的简历关键词版本。

关键词：

- AI Agent；
- RAG；
- Tool Calling；
- Function Calling；
- LangGraph；
- FastAPI；
- 向量数据库；
- Embedding；
- LLMOps；
- Agent Workflow；
- Docker；
- 评测 Harness；
- 文档智能；
- 结构化输出。

---

## 任务 23：投递岗位筛选标准

优先投递这些标题：

- AI Agent 开发实习生；
- 大模型应用开发实习生；
- Agent 工程师；
- RAG 工程师；
- 大模型平台开发实习生；
- LLMOps 实习生；
- AI 应用开发工程师；
- 企业智能体开发实习生。

谨慎投递：

- 基础大模型训练；
- 多模态算法研究；
- 强化学习算法岗；
- 推理引擎优化；
- 纯后端 Java / Go 岗。

---

## 任务 24：暂时不要深挖的内容

一个月内先不要把主时间投入：

- 从零训练大模型；
- 深入 RLHF / PPO / GRPO；
- 大规模分布式训练；
- 复杂 Kubernetes；
- CUDA 内核优化；
- 复杂前端；
- 论文复现型项目。

---

## 完成标准

达到以下标准即可开始投递：

- 项目能一键运行；
- 至少有 3 个完整 Agent 运行案例；
- 至少有 5 个工具；
- 至少有 10 条评测样例；
- README 完整；
- 有架构图；
- 有 Dockerfile；
- 简历中能写出 3 到 4 条项目经历；
- 能 2 分钟讲清项目；
- 能回答 Agent、RAG、Tool Calling、LangGraph、评测和部署相关基础问题。
