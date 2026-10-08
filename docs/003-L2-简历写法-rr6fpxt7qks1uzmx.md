# 简历写法

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/rr6fpxt7qks1uzmx
- Slug: rr6fpxt7qks1uzmx
- Doc ID: 270948585
- 层级: 2
- 字数: 929
- 创建时间: 05/22/2026 12:32:44
- 更新时间: 07/06/2026 02:18:00
- 发布时间: 06/30/2026 06:56:58
- 内容更新时间: 06/30/2026 06:56:58

## 标题结构

- h2: AGI-saber 智能体助手
- h3: 项目概述
- h3: 项目亮点
- h4: ● 混合检索 RAG 架构
- h4: ● RAG 全链路真实评测
- h4: ● 自研记忆系统
- h4: ● 动态图 ReAct Runtime：
- h4: ● Harness 容错执行引擎：
- h4: ● 多层安全校验与沙箱执行机制：

## 资源链接



## 正文

AGI-saber 智能体助手项目概述面向程序员的 AI 学习助手，帮助用户完成技术学习、知识沉淀与个人成长闭环项目亮点● 混合检索 RAG 架构结合 Milvus 向量检索、Elasticsearch BM25 关键词检索与 Neo4j Knowledge Graph 三路召回，通过 RRF（Reciprocal Rank Fusion）融合排序提升召回稳定性与检索准确率；自定义 Markdown 清洗、Chunk 切片、Entity/Relation 抽取与多跳图扩散检索，实现程序员技术知识场景下的 Hybrid GraphRAG。● RAG 全链路真实评测基于 1250 篇掘金技术文档构建企业级 RAG Benchmark，完成 10000+ chunk 的 Retrieval / Generation 全链路评测；设计 50 条 Golden Queries 黄金测试集，从 Recall@K、MRR、NDCG、HitRate 等维度，对国内主流向量模型进行 Dense / BM25 / Hybrid Retrieval 实测，最终基于 Hybrid + RRF 将 NDCG@10 提升至 0.83；同时基于 Exploding Gradients RAGAS 框架完成生成阶段评测，对主流对话模型进行 Faithfulness、Answer Relevancy、Context Precision、Context Recall 多维分析，最终选择 Doubao-Embedding-Large 与 Doubao-Seed-2.0-lite 作为程序员知识库场景下的最优 RAG 方案。● 自研记忆系统设计并实现 Agent 分层记忆系统，融合 Mem0 与 ByteDance Viking 思想，构建短期会话记忆、长期语义记忆、图记忆与运行时状态记忆四层架构；基于 Neo4j 构建 Memory Graph，通过 FOLLOWS / SIMILAR_TO 等关系维护记忆演化链与语义关联，并设计 Fact Extraction、Hash/Embedding 双阶段去重、Graph-Aware Consolidation、TTL + Importance 动态生命周期管理机制，实现长期用户画像沉淀与 Runtime Context Assembly（Planner State / Tool State / Task Memory 动态上下文拼装），提升 Agent 长上下文记忆能力与任务连续性。● 动态图 ReAct Runtime：设计并实现基于 DAG（有向无环图）的 Agent Workflow Runtime，将传统串行 ReAct（Thought → Action → Observation）升级为“可调度、可并行、可恢复”的动态图执行框架；通过任务拆分、节点依赖建模、拓扑排序与入度控制实现动态调度，对搜索、记忆检索、网页抓取等无依赖节点进行并行执行，并引入 Race Strategy 支持多搜索源、多模型、多检索策略并发竞速，优先采用最快结果，相比传统链式 Agent 显著降低复杂任务整体耗时并提升吞吐能力与容错性。● Harness 容错执行引擎：设计并实现面向 AI Agent 的 Harness Runtime，将 Agent 从Prompt + Tool Calling升级为有状态、可恢复的可靠执行系统；针对 LLM / Tool / MCP / RAG 等长链路不稳定 IO 场景，构建超时控制、节点重试、软失败恢复、Fallback Strategy等容错机制，并通过统一结构化 Tool Result Schema 与显式状态机实现多步骤一致性与工作流可编排执行。● 多层安全校验与沙箱执行机制：设计 Docker Sandbox Runtime，对代码执行、Shell Tool 与外部 IO 进行权限控制与资源隔离；通过 --network none、--read-only、--cap-drop ALL、no-new-privileges 等容器安全策略限制潜在危险操作，并结合 Tool Risk Classification（Safe / Warn / Block）、输入校验、敏感命令拦截与 Kafka Audit Log 构建多层安全防护体系，提升 Agent Runtime 的安全性、可观测性与稳定性。
