# 从 Static DAG 到 Plan-and-ReAct

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/ltm0t3337f5k0glu
- Slug: ltm0t3337f5k0glu
- Doc ID: 276023859
- 层级: 4
- 字数: 816
- 创建时间: 07/01/2026 16:11:50
- 更新时间: 07/02/2026 00:40:17
- 发布时间: 07/01/2026 16:22:19
- 内容更新时间: 07/01/2026 16:22:19

## 标题结构

- h2: 1. Plan-and-ReAct新增Replanner
- h2: 2. 架构对比
- h3: 2.1 改造前：Static DAG
- h3: 2.2 改造后：Plan-and-ReAct
- h2: 3. 调度状态机
- h2: 4. 节点生命周期
- h2: 5. AddNodes 的入度处理
- h2: 6. Replanner Prompt 设计

## 资源链接



## 正文

1. Plan-and-ReAct新增Replanner项目里的 mode = "react" 一直被叫作 ReAct，但从调度语义看，它更接近 Static DAG Plan-and-Execute：Planner LLM 一次性输出全图Executor 按拓扑分层并行执行Generator LLM 一次性合成答案中间过程没有 LLM 决策介入这与 Yao et al. 2022 定义的 ReAct（Reasoning 与 Acting 交替循环）在语义上是相反的。为了让这套调度真正具备"看观察 → 决定下一步"的能力，本次改造引入 Replanner，把架构从 Static DAG 升级为 Plan-and-ReAct（Dynamic DAG）。2. 架构对比2.1 改造前：Static DAG问题：图一旦生成就是死的，中途出错只能靠 retry / race 兜底，不能让 LLM 重新规划。2.2 改造后：Plan-and-ReAct关键差异：Executor 的循环里插入了 Replanner 决策点，让 LLM 能基于 observations 追加节点，实现真正的 "Reasoning + Acting 交替"。3. 调度状态机4. 节点生命周期单个 Node 的状态流转，Replanner 通过 AddNodes 引入的新节点也遵循同一状态机：5. AddNodes 的入度处理TaskGraph.AddNodes 是让图能"活"起来的核心。它需要处理三种依赖场景：关键点：如果新节点依赖的是已完成的节点，入度不加，下一轮 ReadyNodes 立即返回它——这是"追加立即执行"的机制来源。6. Replanner Prompt 设计Replanner 与 Planner 的差异不是模型不同，而是输入与判断规则不同：维度Planner (llmPlanGraph)Replanner (llmReplan)输入Query + Tool 列表Query + Tool 列表 + 当前图快照 + 观察结果触发时机请求开始每层执行完 / 节点失败输出语义全图增量节点（可为空）提示词侧重"选出需要的工具，标依赖""现有观察够不够？不够再补"ID 命名n1, n2, ...r1-*, r2-*, ...（避免冲突）Replanner 的判断规则（写在 prompt 里）：观察已足够 → 返回 []需要基于观察进一步查询/加工 → 追加节点追加节点的 depends_on 可以指向图中任何已存在节点 id不重复已有节点做过的事单次最多追加 3 个节点prompt：
