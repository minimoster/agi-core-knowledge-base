# 任务 DAG 设计

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/lrfvxx45p6qm1g9p
- Slug: lrfvxx45p6qm1g9p
- Doc ID: 274654838
- 层级: 4
- 字数: 2181
- 创建时间: 06/21/2026 13:42:05
- 更新时间: 06/30/2026 09:28:16
- 发布时间: 06/30/2026 09:28:15
- 内容更新时间: 06/30/2026 09:28:15

## 标题结构

- h2: 一. 整体链路
- h3: 1.1 DAG 在 Agent 中处于什么位置
- h3: 1.2 三个核心抽象
- h3: 1.3 Node 的关键字段
- h3: 1.4 三阶段执行链路
- h3: 1.5 端到端时序
- h2: 二. 本项目的独到之处
- h3: 2.1 运行时 DAG 而非编译时 DAG
- h3: 2.2 同层竞速（RaceGroup）
- h3: 2.3 拓扑分层 vs 单步推进

## 资源链接



## 正文

一. 整体链路1.1 DAG 在 Agent 中处于什么位置DAG 只服务于 ReAct 模式——多步推理 + 多工具协作的场景。其他三个模式（chat/tool/rag）是单步或线性流程，不走 DAG。1.2 三个核心抽象抽象职责Node单个执行单元（工具调用 / 子 Agent / 思考 / 聚合）TaskGraph有向无环图，带邻接表 + 入度表 + 拓扑层缓存GraphRuntime调度器：拓扑分层 + 信号量并发 + 竞速执行1.3 Node 的关键字段两个超出常规 DAG 的字段：RaceGroup ：同组节点竞速执行，首个成功的获胜，其余被取消AgentName + Goal ：节点可以是子 Agent，不只是工具1.4 三阶段执行链路1.5 端到端时序二. 本项目的独到之处2.1 运行时 DAG 而非编译时 DAGLangGraph：图结构在代码里写死，运行时不变。本项目：图结构由 LLM 动态产出——同一个用户问题，不同的 LLM 输出可能产生完全不同的图。好处：Agent 真正"会规划"——不是按固定流程走工具集变化时 Planner 自动适配，不用改代码用户问"研究 X 写报告"，Planner 自动生成 research → writer → review 三节点链代价：需要降级路径（LLM 解析失败 → 规则 → 全并行）调试比 LangGraph 难——图结构每次都可能不同2.2 同层竞速（RaceGroup）这是 LangGraph 完全没有的能力。场景：用户问"X 是什么"，可以同时调 search_web 和 rag_search——谁先返回用谁。Planner 输出：调度细节：好处：延迟降低：N 路并发，wall-clock = min(各路延迟)可靠性提升：单源故障不致命，其他源继续Cypher 级语义：First-success-wins 比 LangGraph 的"全等"语义更适合 agent 场景例子：用户问"研究 React 18 写一份报告并保存到知识库"——Planner 生成：2.3 拓扑分层 vs 单步推进LangGraph：每次推进一个"step"，状态机驱动，需要 conditional edge 决定下一步。优点：可控性强、便于回放缺点：并发需要手动 Send，复杂度上来本项目：Kahn 算法分层，同层自动并行。调度伪代码：好处：自动并行：开发者不用思考"哪些可以并发"延迟下界：wall-clock = sum(各层最长节点) 而非 sum(全部节点)简单确定：一层完成才进下一层，状态清晰
