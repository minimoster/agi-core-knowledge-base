# 动态图ReAct和串行链式ReAct

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/rdbcbr1vy2oaenr6
- Slug: rdbcbr1vy2oaenr6
- Doc ID: 271038970
- 层级: 4
- 字数: 1510
- 创建时间: 05/23/2026 12:36:51
- 更新时间: 07/09/2026 09:21:38
- 发布时间: 05/28/2026 10:57:19
- 内容更新时间: 05/28/2026 10:57:19

## 标题结构

- h2: 串行链式 ReAct（Linear / Sequential ReAct）
- h3: 适合场景
- h2: 动态图 ReAct（Graph-based ReAct）
- h3: 为什么动态图更先进
- h3: 动态图 ReAct 的关键组件
- h2: 区别
- h3: 串行链式 ReAct
- h3: 动态图 ReAct
- h2: 业界演进趋势
- h2: 动态图的实现方法
- h3: 经典 Kahn 算法
- h3: 现代 Runtime 会怎么做
- h3: Agent Runtime 比普通 DAG 多的东西
- h3: 真正工业级 Scheduler
- h1: ​

## 资源链接



## 正文

前言：👀这篇文档之前建议去力扣刷一下课程表（）这道题，会对你有意想不到的帮助！串行链式 ReAct（Linear / Sequential ReAct）传统 ReAct 本质：即：一步一步串行推进当前步骤完成后才能进入下一步上下文是线性的每次只维护一个执行路径典型特点：特征串行链式 ReAct执行方式单链路串行状态结构Linear Context推理模式单线程思考Tool调用顺序调用容错弱可恢复一般并行能力无复杂任务容易爆context适合场景适用于：简单问答单工具任务一次性推理短链路 Agent例如：这是经典 ReAct。动态图 ReAct（Graph-based ReAct）动态图 ReAct 的核心：不是“链”。而是：结构类似：它本质是：核心区别维度串行链式 ReAct动态图 ReAct执行结构链表DAG/Graph推理方式单路径多路径Context单上下文节点级上下文Tool调用串行可并行状态管理Prompt里硬塞Runtime State容错Retry困难节点恢复长任务容易崩可持续执行Memory临时上下文图状态持久化调度无Scheduler适合Chat AgentProduction Agent为什么动态图更先进因为真实 Agent 任务不是线性的。例如：实际上会拆成：这里天然就是 DAG：不是线性链。动态图 ReAct 的关键组件1. Planner（任务规划）先把任务拆图：例如：2. Runtime State每个节点有独立状态：而不是全部塞 prompt。3. Scheduler（调度器）决定：哪些节点可以执行哪些依赖完成哪些失败重试哪些并行类似：思想。4. Memory Graph记忆不再只是：而是：例如：直接图连接。一个直观例子串行 ReAct像：动态图 ReAct像：分布式调度系统区别串行链式 ReAct本质：动态图 ReAct本质：即：LLM 不再直接控制全部流程。而是：这是现代 Agent 的核心方向。业界演进趋势大致路线：代表：OpenAI Deep ResearchAnthropic Computer UseLangChain LangGraphMicrosoft AutoGenTemporal Technologies Temporal Agent Runtime基本都在：而不是继续做 Prompt chaining。动态图的实现方法是业界 DAG Runtime 最常见的一种实现思路。本质流程：但真正的 Agent Runtime / Workflow Engine 里，会在这个基础上再加：状态机调度器优先级并行池重试机制Future/Promise依赖追踪最后就演化成：最基础 DAG 执行模型例如：数据结构一般：图：入度：经典 Kahn 算法Step1 初始化队列把：的节点放进去。即：Step2 执行节点执行：然后：这条边被“删除”。于是：再执行：则：于是：进入队列。为什么很多系统用小顶堆因为：时，需要：例如：策略小顶堆排序依据BFSdepth优先级priority最早创建timestamp最短任务优先costAI Agentimportance score所以：而不是普通 queue。现代 Runtime 会怎么做真正复杂的是：而是：例如：于是 Scheduler 会：这已经接近：Ray DAGAirflowPrefectTemporalLangGraph了。Agent Runtime 比普通 DAG 多的东西普通 DAG：但 Agent：于是节点状态会变成：不是单纯函数调用。真正工业级 Scheduler一般会维护：1. Ready Queue2. Running Set3. Finished Set4. Failed Set5. Dependency Map​
