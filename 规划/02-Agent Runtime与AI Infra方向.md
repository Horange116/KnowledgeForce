# Agent Runtime 与 AI Infra 方向

## 1. 方向定位

Agent Runtime 与 AI Infra 方向是 Agent 应用工程之后的中长期升级方向。

核心目标是：

> 构建支撑 Agent 稳定运行、可扩展、可观测、可调度、可部署的底层运行时和基础设施。

如果 Agent 应用工程关注“做一个能解决业务问题的 Agent”，那么 Agent Runtime / AI Infra 关注的是“让大量 Agent 在真实环境中稳定、高效、低成本地运行”。

---

## 2. 典型岗位名称

- Agent Runtime 工程师
- Agent Framework 工程师
- Agent Infra 工程师
- Agent 平台工程师
- AI 原生应用平台工程师
- 多 Agent 框架工程师
- C 端 Agent 框架工程师
- AI Infra 工程师
- 云原生 Agent 平台工程师
- 异构调度系统工程师

---

## 3. 典型业务场景

### 3.1 Agent 核心运行时

- Agent Loop
- 消息准备
- System Prompt 动态组装
- 流式 API 通信
- 工具并发调度
- 工具结果收集
- 异常兜底
- 多轮对话状态管理

### 3.2 Context 与 Memory

- 多层 Context 管理
- Context Window 控制
- Prompt Caching
- Token 精确计算
- 多级压缩策略
- 长短期记忆
- 跨会话持久化记忆
- 自动记忆提取
- 离线记忆巩固
- Memory Scope 设计

### 3.3 Hooks 与个性化配置

- Hooks 系统
- 项目级指令注入
- 用户偏好学习
- 个性化行为配置
- 工具调用前后钩子
- 运行时策略注入

### 3.4 多 Agent 协作

- Fork 子 Agent
- AgentTool 嵌套
- 并发子任务
- Planner / Worker 架构
- 多 Agent 调度
- 多 Agent 结果聚合
- 任务拆解与状态管理

### 3.5 云原生 Agent Infra

- Agent 瞬时爆发负载
- 毫秒级冷启动
- 高密度 MicroVM 隔离
- Scale-to-Zero
- CPU / GPU 异构资源调度
- 万级节点调度
- 模型托管
- AI 原生应用平台
- Agent 全生命周期管理平台

---

## 4. 核心技术能力

### 4.1 Agent Runtime 基础

- Agent Loop
- Tool Registry
- Tool Executor
- Context Manager
- Memory Manager
- Planner
- Executor
- Runtime State
- Message Schema
- Structured Output
- Error Handling

### 4.2 服务可靠性

- 超时控制
- 重试机制
- Circuit Breaker
- 降级策略
- 静默失败检测
- 幂等设计
- 任务恢复
- 高可用服务设计
- 失败兜底

### 4.3 流式与并发

- Streaming API
- 首 token 延迟优化
- 流式输出流畅度
- 异步并发编程
- Python async / await
- TypeScript 异步编程
- 工具并发调度
- 并发结果收集

### 4.4 可观测与评测对齐

- Agent 埋点规范
- Trace 日志
- 工具调用日志
- Latency 统计
- Token / Cost 统计
- Tool Success Rate
- Eval 数据采集
- OpenTelemetry
- Prometheus
- 日志回放

### 4.5 AI Infra 与云原生

- Docker
- Kubernetes
- MicroVM
- Serverless
- Scale-to-Zero
- CPU / GPU 调度
- 异构资源管理
- 模型推理服务
- vLLM
- TensorRT-LLM
- 分布式系统
- 操作系统基础

---

## 5. 和 Agent 应用工程的区别

| 维度 | Agent 应用工程 | Agent Runtime / AI Infra |
|---|---|---|
| 关注点 | 解决具体业务问题 | 支撑大量 Agent 稳定运行 |
| 典型产出 | 文档审查 Agent、客服 Agent、搜索 Agent | Agent 框架、Runtime、平台、调度系统 |
| 核心能力 | RAG、Tool Calling、Workflow、FastAPI | Agent Loop、Context、Memory、并发、调度、可观测 |
| 用户 | 业务用户、运营、客服、企业员工 | 开发者、平台团队、其他 Agent 应用 |
| 难点 | 业务理解与效果闭环 | 稳定性、性能、扩展性、资源效率 |

---

## 6. 推荐学习顺序

### 阶段一：实现最小 Agent Runtime

实现：

- Message 数据结构
- Tool Schema
- Tool Registry
- Agent Loop
- 工具调用与结果回填
- 最大轮次限制
- 错误处理

### 阶段二：补 Context 管理

实现：

- Token 估算
- 上下文截断
- 对话摘要
- 检索式上下文注入
- Prompt Caching 理解
- 多级压缩策略

### 阶段三：补 Memory

实现：

- 用户偏好记忆
- 项目记忆
- 任务记忆
- 跨会话持久化
- Memory Scope
- 自动记忆提取

### 阶段四：补 Hooks 与日志

实现：

- Before Tool Hook
- After Tool Hook
- Before LLM Hook
- After LLM Hook
- Trace 日志
- Eval 采样日志

### 阶段五：补多 Agent

实现：

- Planner Agent
- Worker Agent
- Critic Agent
- Evaluator Agent
- 任务拆分
- 并发执行
- 结果聚合

### 阶段六：补基础设施

学习：

- Docker
- Kubernetes 基础
- Serverless / Scale-to-Zero
- vLLM
- GPU 调度基础
- OpenTelemetry
- Prometheus

---

## 7. 推荐项目

### 7.1 Mini Agent Runtime

从零实现一个最小 Agent Runtime：

- 支持工具注册
- 支持多轮循环
- 支持 JSON 工具调用
- 支持错误处理
- 支持 Trace 日志

### 7.2 Context Manager Demo

实现一个上下文管理器：

- Token 预算控制
- 上下文压缩
- 历史摘要
- 关键事实保留
- RAG 上下文注入

### 7.3 Memory System Demo

实现一个 Memory 系统：

- 用户级记忆
- 项目级记忆
- 任务级记忆
- 自动提取
- 离线巩固
- 记忆召回评测

### 7.4 Multi-Agent Orchestrator

实现一个多 Agent 调度器：

- Planner 拆任务
- Worker 执行任务
- Critic 检查结果
- Evaluator 打分
- Orchestrator 汇总输出

### 7.5 Agent Observability Demo

实现一套可观测链路：

- run_id
- tool trace
- latency
- token cost
- error reason
- eval result
- 日志回放

---

## 8. 简历关键词

- Agent Runtime
- Agent Loop
- Tool Registry
- Tool Executor
- Context Management
- Memory Scope
- Prompt Caching
- Token Budget
- Hooks
- Streaming API
- Async Tool Calling
- Multi-Agent
- Observability
- Trace
- Circuit Breaker
- Retry
- Fallback
- Scale-to-Zero
- MicroVM
- Kubernetes
- vLLM
- GPU Scheduling

---

## 9. 当前阶段建议

短期不要把主时间投入过深的云原生和调度系统。

当前更合理的顺序是：

1. 先完成 Agent 应用工程项目。
2. 在项目中加入简化版 Agent Loop、Trace、Eval、Memory、工具注册。
3. 再从项目中抽象出 Mini Runtime。
4. 最后补 MicroVM、Scale-to-Zero、GPU 调度、Kubernetes 等底层能力。

---

## 10. 投递建议

适合中期投递：

- Agent Runtime 工程师
- Agent 框架工程师
- Agent 平台工程师
- AI Infra 工程师
- AI 原生应用平台工程师

当前如果项目还没有成熟，不建议直接主投：

- MicroVM / Serverless Runtime 岗
- 万级节点异构调度岗
- GPU 资源调度岗
- 纯云原生基础设施岗
- 系统顶会研究岗