# Agent 应用工程方向

## 1. 方向定位

Agent 应用工程方向是当前主攻方向。

核心目标是：

> 把大模型能力嵌入真实业务流程，构建可运行、可评测、可部署、可迭代的 AI Agent 应用系统。

这个方向不是单纯写 Prompt，也不是只做聊天机器人，而是要完成从业务场景拆解、RAG、工具调用、Agent Workflow、结构化输出、评测、部署到用户交互的完整工程闭环。

---

## 2. 典型岗位名称

- AI Agent 开发工程师
- 大模型应用开发工程师
- RAG 工程师
- Deep Research 工程师
- AI 搜索总结 Agent 工程师
- 智能客服 Agent 工程师
- 企业知识库 Agent 工程师
- 企业文档审查 Agent 工程师
- 电商运营 Agent 工程师
- 行业 Agent 应用工程师
- AI Coding / 工程研发 Agent 工程师

---

## 3. 典型业务场景

### 3.1 企业流程智能化

- 企业文档审查
- 合同审查
- 制度一致性检查
- 内部知识库问答
- 跨文档信息核对
- 审批辅助
- 质检辅助

### 3.2 电商与运营 Agent

- 商家运营 Agent
- 商品运营 Agent
- 智能客服 Agent
- 售前推荐
- 活动解读
- 商品对比
- 售后排障
- 物流追踪
- 店铺 AI 托管

### 3.3 AI 搜索与 Deep Research

- 搜索总结 Agent
- 长文本理解
- 跨文档信息融合
- Query 理解
- 结构化总结
- 引用检查
- 自动化研究工具

### 3.4 工程研发 Agent

- AI Coding
- Code Review Agent
- 需求分析助手
- 测试分析助手
- 项目管理助手
- AI Driven IDE
- 多 Agent 软件开发协作

---

## 4. 核心技术栈

### 4.1 Agent 应用核心

- Prompt Engineering
- System Prompt 设计
- Function Calling / Tool Calling
- Agent Workflow
- Planning
- Reasoning
- 多轮对话状态管理
- 结构化输出
- Pydantic Schema
- JSON 校验与自动修复

### 4.2 RAG 与知识库

- 文档解析
- Chunk 切分
- Embedding
- 向量数据库
- Faiss / Chroma / Milvus
- Top-K 检索
- Hybrid Search
- Rerank
- 引用溯源
- 跨文档一致性检查
- Graph RAG 基础

### 4.3 Agent 框架

- LangChain
- LangGraph
- LlamaIndex
- AutoGen
- CrewAI
- Dify
- MCP 基础

### 4.4 工程化能力

- Python
- FastAPI
- Streamlit / 简单前端
- Docker
- Git
- Linux
- REST API
- 日志与 Trace
- 成本统计
- 错误处理
- 重试与降级
- 测试用例
- Eval Harness

---

## 5. 当前主项目

主项目建议固定为：

> 企业文档审查 Agent：基于 RAG、工具调用和评测 Harness 的多文档一致性分析系统。

### 5.1 项目目标

让用户上传多份企业文档，Agent 能够完成：

- 文档解析
- 信息检索
- 关键事实抽取
- 跨文档一致性检查
- 风险点识别
- 引用来源展示
- 结构化审查报告生成
- 评测与日志追踪

### 5.2 推荐目录结构

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
├── frontend/
├── README.md
├── Dockerfile
└── requirements.txt
```

### 5.3 必须实现的工具

- `read_file_tool`：读取文件
- `search_doc_tool`：检索文档片段
- `extract_claims_tool`：抽取事实、指标、时间、实体
- `compare_sections_tool`：比较片段是否一致
- `generate_report_tool`：生成结构化审查报告
- `citation_check_tool`：检查引用是否存在
- `json_validate_tool`：检查输出格式

### 5.4 推荐 Workflow

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

---

## 6. 评测与质量体系

Agent 应用工程必须重视评测，而不是只做 Demo。

### 6.1 评测集

至少准备：

- 普通事实问答
- 跨文档一致性问题
- 缺失信息问题
- 无关问题
- 格式约束问题
- 工具调用失败问题
- 多轮追问问题

### 6.2 指标

- Answer Correctness
- Citation Hit Rate
- Tool Success Rate
- Format Valid Rate
- Latency
- Failure Reason
- Retrieval Recall
- 引用正确率
- 结构化输出合法率

### 6.3 日志 Trace

每次运行记录：

- run_id
- 用户输入
- 检索到的 chunks
- 调用过的工具
- 每个工具输入输出
- LLM 输出
- 总耗时
- 错误信息
- 成本统计

---

## 7. 学习顺序

### 阶段一：RAG 基础

- 文档解析
- Chunk 切分
- Embedding
- 向量检索
- 引用溯源

### 阶段二：Tool Calling

- 工具 Schema
- 工具注册
- 工具调用
- 工具失败处理
- 工具日志

### 阶段三：LangGraph Workflow

- State 设计
- Node 设计
- 条件分支
- 失败重试
- 结构化输出

### 阶段四：FastAPI 服务化

- 上传文档
- 建立索引
- RAG 问答
- Agent 审查
- 日志查看

### 阶段五：Eval 与项目包装

- 评测集
- 自动评测脚本
- 评测报告
- README
- Docker
- 简历项目描述

---

## 8. 简历关键词

- AI Agent
- RAG
- Tool Calling
- Function Calling
- LangGraph
- FastAPI
- 向量数据库
- Embedding
- Agent Workflow
- 结构化输出
- Pydantic
- Trace
- Eval Harness
- Citation Check
- Docker
- 企业文档审查
- 多文档一致性分析
- Deep Research

---

## 9. 投递优先级

优先投递：

- AI Agent 开发实习生
- 大模型应用开发实习生
- RAG 工程师
- Deep Research 工程师
- 智能客服 Agent 工程师
- 企业知识库 Agent 工程师
- AI 搜索 Agent 工程师
- 工程研发 Agent 工程师

谨慎投递：

- 纯基础大模型训练岗
- 纯强化学习算法岗
- 纯多模态研究岗
- 纯云原生基础设施岗
- 纯产品经理岗

---

## 10. 完成标准

达到以下标准即可开始投递：

- 项目能一键运行
- 至少有 3 个完整 Agent 运行案例
- 至少有 5 个工具
- 至少有 10 条评测样例
- README 完整
- 有架构图
- 有 Dockerfile
- 有执行日志和评测报告
- 简历能写出 3 到 4 条项目亮点
- 能 2 分钟讲清项目
- 能回答 Agent、RAG、Tool Calling、LangGraph、Eval、部署相关基础问题