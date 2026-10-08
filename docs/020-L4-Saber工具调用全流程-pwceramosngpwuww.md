# Saber工具调用全流程

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/pwceramosngpwuww
- Slug: pwceramosngpwuww
- Doc ID: 275954646
- 层级: 4
- 字数: 5930
- 创建时间: 07/01/2026 06:01:01
- 更新时间: 07/09/2026 09:21:28
- 发布时间: 07/09/2026 09:21:26
- 内容更新时间: 07/09/2026 09:21:26

## 标题结构

- h2: 前置知识
- h2: 全文概要
- h2: 5 分钟先建立直觉
- h2: 1. 整张工具调用全景图
- h2: 2. 第 1 步：工具是什么 —— Tool 数据结构
- h2: 3. 第 2 步：工具放在哪 —— 并发安全的 toolRegistry
- h2: 4. 第 3 步：路由决策 —— 4 种模式分发
- h3: 路径 A：前端显式指定（最准确）
- h3: 路径 B：关键词启发式（自动）
- h2: 5. 第 4 步：单工具模式 —— 最简单的路径
- h2: 6. 第 5 步：ReAct 模式 —— 全套工具调用流程 ★ 核心
- h3: Step 1: Planner LLM 决定调谁
- h3: Step 2: 构建 TaskGraph
- h3: Step 3: GraphRuntime 并行调度
- h3: Step 4: 竞速执行（First-success-wins）★
- h3: Step 5: Generator LLM 综合
- h2: 7. 第 6 步：执行单节点 —— 重试 + 中断 + 事件推送
- h2: 8. RAG 当作 Tool 的特殊之处 ★
- h3: 特殊点 1：注册位置不在 builtin
- h3: 特殊点 2：依赖 Loaded 状态做前置检查
- h3: 特殊点 3：丢弃第二个返回值
- h3: 特殊点 4：RaceGroup 设计
- h3: 特殊点 5：自反性（RAG 既能查、又能写）
- h2: 9. 整合：一个真实例子从头跑
- h2: 10. ​一句话总览
- h2: 11. 面试 5 连问（看完应该能答）

## 资源链接

- a: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/kos09ra4r7uh4tse?singleDoc# 聊聊MCP

## 正文

前置知识1.聊聊MCP全文概要这一章把 Saber 项目"如何让 LLM 调工具"讲清楚。读完后，你应该能回答：项目有没有 function calling、它是怎么实现的、为什么这么实现、RAG 怎么变成一个 tool 被 LLM 调用、整个流程从 query 到答案中间发生了什么。5 分钟先建立直觉LLM 是一颗"装在玻璃罐里的大脑"——它只会基于训练数据吐文本，不能：查实时天气跑 Linux 命令读你电脑上的文件检索你的私人知识库但用户问的问题往往就需要这些能力。怎么办？给它一组"机械臂"，让它告诉系统"我要调用 get_weather('北京')"，系统替它调，把结果塞回去给它继续说。这就是 Tool Calling（工具调用），也叫 Function Calling。📦 额外知识：Tool Calling 的两种风格原生 function calling：OpenAI / Anthropic 在 API 层提供 tools 字段，LLM 输出结构化 tool_calls JSON。优点：稳定，模型专门训练过。缺点：协议绑死、表达能力受限。自实现 JSON 风格（本项目）：把 tools 描述拼进 prompt，让 LLM 吐 JSON，自己解析。优点：换 LLM 不用改协议、能塞 depends_on / race_group 这种私货。缺点：吃 prompt token、需要做大量 fallback。Saber 走的是第二条路。下面顺着代码讲清楚为什么。1. 整张工具调用全景图​下面分 6 章一步步讲解。2. 第 1 步：工具是什么 —— Tool 数据结构最底层抽象在 internal/domain/tool/tool.go：类比：一个 Tool 就是一张"名片 + 一个按钮"：名片：Name + Description + Parameters 给 LLM 看（"我叫 get_weather，能查天气，需要 city 参数"）按钮：Execute 给系统按（真正执行）注意 json:"-"：Execute 是 Go 函数指针，不能也不应该序列化给 LLM。LLM 只能看到名片决定调用，按钮永远在本地系统里。Param 结构：📦 额外知识：为什么 params 是 map[string]interface{} 不是强类型 struct？因为工具种类无穷多——get_weather 要 city，exec_command 要 command，没法用一个统一 struct。interface{} + map 是 Go 实现"动态参数表"的标准模式，代价是 Execute 内部要手动 type assert（p["city"].(string)），但灵活性换来了"加一个工具不用改框架"。3. 第 2 步：工具放在哪 —— 并发安全的 toolRegistryLLM 可能同时被多路调用（HTTP 接口、SSE 流、ReAct 循环），它们都要读工具表。Go 原生 map 并发读写直接 panic，所以包装一层。看 internal/application/chat/tool_registry.go：三个核心方法：方法何时调用锁registerAgent 初始化注册 / 动态加 MCP写锁snapshot每次 query 拿一份 map 浅拷贝供后续用读锁filter前端勾选了部分工具时按白名单筛读锁为什么用 snapshot 而不是直接共享 map？调用方拿到 snapshot 后解锁即返回，后续遍历 map 无需持锁——避免 ReAct 循环（可能跑十几秒）一直握着读锁，挡住 MCP 工具动态注册。这是个标准的**"读时拷贝、写时上锁"模式**。💡 一句话记住：Tool 是"名片 + 按钮"。toolRegistry 用 RWMutex 包 map 提供 snapshot，调用方拿快照后无锁遍历，避免长任务挡住注册。4. 第 3 步：路由决策 —— 4 种模式分发不是所有 query 都需要走工具调用。runtime_process.go 的 routeDecide 分两条路径：路径 A：前端显式指定（最准确）前端勾了"使用工具 search_web + rag_search" → 直接走 react 模式。这是最干净的路径，因为用户明示了。路径 B：关键词启发式（自动）具体规则在 infra_router.go：为什么是关键词不是 LLM？ 路由是入口，要快、便宜、确定。LLM 路由要再多花一次 API 调用 + 1-2 秒延迟。关键词错了大不了走错路径，错路径里还有降级。📦 额外知识：为什么 RAG 是 "needTool / needReAct 都不命中" 才走？a.rag.Loaded && !a.needTool(query) && !a.needReAct(query)这是个优先级设计：如果 query 明显是问天气 / 时间，没必要先去 RAG 翻一遍知识库；如果是复合任务（写报告），ReAct 模式里 Planner 会自动把 rag_search 排进图里，不必单独走 RAG 模式。RAG 模式只服务"看起来像问私人知识库的纯检索问题"。5. 第 4 步：单工具模式 —— 最简单的路径走 mode=tool 时调 mode_tool.go，整段不到 40 行：tool.Decide 是纯关键词选工具：核心权衡：单工具模式整体只调 1 次 LLM（最后那次综合），延迟低；代价是工具选择是规则的，不够智能。"查天气"能命中，"我女朋友所在城市今天会下雨吗"就只能走规则兜底（取首个工具）或干脆走不通。💡 一句话记住：单工具 = 1 次规则选工具 + 1 次工具执行 + 1 次 LLM 综合。简单、快、便宜，适合明确的单步任务。6. 第 5 步：ReAct 模式 —— 全套工具调用流程 ★ 核心mode=react 时调 mode_react.go 的 runReAct，分 5 步：Step 1: Planner LLM 决定调谁llmPlanGraph 做的事（plan_graph.go）：📦 额外知识：LLM 输出不稳定怎么办？6 道防线Prompt 工程：枚举可选值、给示例、约束"只输出 JSON"输出清洗：剥 ```json 包裹、剥 <|FunctionCallBegin|> 特殊 token三档 schema 解析：理想格式 → 旧格式 → 原生 function calling 格式白名单过滤：编出来的不存在工具直接丢整体降级：解析全失败 → 走纯规则 rulePlanNodes图层兜底：DAG 有环 → 清空依赖降级全并行这是工程师答 "LLM 输出不可靠你怎么处理" 的标准范式。Step 2: 构建 TaskGraphTaskGraph 做两件事：拓扑排序：把节点分成层，同层节点可以并行校验：检测环、检测 depends_on 指向不存在的节点📦 额外知识：什么是 DAG（有向无环图）？全称 Directed Acyclic Graph。"有向"= A→B 不等于 B→A，"无环"= 没有 A→B→A 这种循环。任务依赖天然是 DAG —— "先调研后写报告" 就是 research_agent → writer_agent。拓扑排序就是 "把 DAG 按依赖顺序排成一串"。Step 3: GraphRuntime 并行调度Execute 逐层调度：并发控制有 3 个旋钮：配置含义MaxParallel信号量 chan struct{}，限制最大同时跑几个RaceTimeoutMs竞速组超时EnableRacing是否启用竞速Step 4: 竞速执行（First-success-wins）★这是 ReAct 模式的杀器。经典场景：search_web + rag_search 同一 RaceGroup="search"两个并发跑谁先返回非错误结果，cancel 另一个失败的标记 StatusSkipped好处：延迟优化：本地命中（rag）比 web 快 → 优先用本地质量保底：rag 没结果 / 出错时 web 兜底不浪费：失败那个被立刻取消，省 LLM 调用费Step 5: Generator LLM 综合llmGenerate 把所有 observation 拼起来交给 LLM：📦 额外知识：为什么要 2 次 LLM 调用？ReAct 模式总是这个结构：Planner LLM（决定调谁）+ Generator LLM（讲人话）。把"决策"和"表达"分离能让两次 prompt 各自最优——Planner 强调结构化输出，Generator 强调自然语言。这是 ReAct 论文（Reason + Act）的核心模式。7. 第 6 步：执行单节点 —— 重试 + 中断 + 事件推送executeSingleNode 是真正"按按钮"的地方：5 件事：① 推事件 → ② 包 run 函数 → ③ 重试循环 → ④ 失败处理 → ⑤ 成功处理。📦 额外知识：什么是 ReAct 模式？ReAct = Reasoning + Acting。每一步分三段：Thought（想）："为了回答这个问题，我需要先查天气"Action（做）：调用 get_weather('北京')Observation（看）：拿到结果 "晴天 22°C"项目里每个节点都会推送这三段事件给前端，让用户看到 LLM 的"思考过程"——这是 ReAct 的可视化卖点。💡 一句话记住：ReAct 模式 = Planner LLM（决定调谁）+ GraphRuntime（DAG 并行 + 竞速）+ Generator LLM（讲人话）。每个节点都走 Thought → Action → Observation 三段事件，前端能可视化。8. RAG 当作 Tool 的特殊之处 ★前面是工具调用的"通用骨架"。RAG 作为 5 个工具中的一员，有 5 个和其他工具不一样的地方。特殊点 1：注册位置不在 builtin普通工具如 get_time / get_weather / search_web 在 internal/infrastructure/tool/builtin.go 的 DefaultTools() 静态注册：RAG 工具不在这里。看 core_agent.go 的 registerBuiltinTools：为什么不能放 builtin？ 因为 Execute 是个闭包，捕获了 a.rag 这个 RAG Engine 实例。builtin 是 infrastructure 层的纯静态工具，拿不到 Engine 实例。所以 RAG 工具必须在 application 层（agent 初始化时）注册。特殊点 2：依赖 Loaded 状态做前置检查get_weather 没有这种"前置不可用"状态，但 RAG 有——空库时调 RAG 没意义，浪费 LLM 调用。这里返回 error 让 LLM 收到信号会自动改用其他工具或告诉用户特殊点 3：丢弃第二个返回值a.rag.Query 原本返回 (answer, []SearchResult)，工具闭包只取 answer，丢弃 results。为什么？因为 SearchResult 里有 chunk_id、相似度分、原文片段等"调试信息"，不该塞进 LLM 上下文——会污染 prompt、浪费 token、还可能让 LLM 误以为这些 ID 是它能引用的东西。命中详情通过 event bus 单独推到前端 SSE，给用户看。📦 额外知识：双通道输出 —— SSE 给前端、回包给 LLM同一个工具返回的数据可能分两路：LLM 通道：精简、自然语言（这条进 prompt）前端通道：详细、结构化（用户能看到引用、来源）把这两路分开是 Agent 系统的成熟做法。否则要么 LLM 被噪声淹没，要么前端没法展示来源。特殊点 4：RaceGroup 设计关键词规则降级时给 rag_search 加：search_web 也是 RaceGroup: "search"。两个工具竞速：特殊点 5：自反性（RAG 既能查、又能写）rag_search 是只读的，但 tool_documents.go 的 write_document 让 LLM 还能往 RAG 里写：LLM 写一份调研报告时可以决定"要不要让这份报告被检索"。这就形成了一个自反闭环：用户问 A → rag_search 查不到 → search_web 查到 → LLM 整理成报告 → write_document(ingest_to_rag=true) → 下次问类似问题 → rag_search 命中这是 "Self-improving RAG" 的雏形——Agent 在用户视角下只是个聊天框，背后它对自己的知识库做 CRUD​💡 一句话记住：RAG 工具相比普通工具，多了 5 个特殊点：闭包绑 Engine 实例、Loaded 状态前置检查、丢弃 results 只回 answer、跟 search_web 竞速、能让 Agent 往里写形成自反闭环。9. 整合：一个真实例子从头跑用户问 "帮我调研一下 K8s service 的几种类型，写成报告保存"：整段流程触发的 LLM 调用次数：1 次 Planner（路由 / 出 plan）N 次子 Agent 内部（research / writer 各自有自己的 LLM）1 次 Generator（合成最终答案）若干次偏好抽取 / 记忆抽取（异步，不阻塞）10. ​一句话总览工具调用 = "给 LLM 装机械臂"。Saber 项目用"自实现 JSON 风格 function calling"——把工具描述拼进 prompt，让 LLM 吐 JSON，自己解析成 DAG，再用 GraphRuntime 做拓扑分层 + RaceGroup 竞速并行执行。四种路由（chat / tool / rag / react）按 query 关键词分发，最复杂的 ReAct 模式走 Planner LLM → DAG → 并行执行 → Generator LLM 五步。RAG 作为 tool 有 5 个特殊点：闭包绑 Engine、Loaded 状态前置检查、丢 results 只回 answer、跟 search_web 竞速、能形成"Agent 给自己写知识库"的自反闭环11. 面试 5 连问（看完应该能答）项目有 function calling 吗？怎么实现的？ 有。但不是 OpenAI 原生的 tools 字段，而是自己实现的 JSON 风格——把工具描述拼进 prompt 文本，让 LLM 吐 JSON，再自己 json.Unmarshal 解析成 planNode。这样做的好处是任何能输出 JSON 的 LLM 都能跑，且能塞 depends_on / race_group 这种原生协议没有的字段LLM 输出不稳定怎么办？ 6 道防线：prompt 工程 → 输出清洗 → 三档 schema 解析 → 工具白名单过滤 → 全失败降级到关键词规则 → 图层 DAG 校验失败降级全并行多个工具怎么并行？有依赖怎么办？Planner 输出 depends_on 字段定义依赖关系，构成 DAG。GraphRuntime 做拓扑排序按层调度，同层节点并行（受 MaxParallel 信号量限制）多个功能相似的工具怎么处理？ 同 race_group 的节点并发竞速，First-success-wins。典型例子是 rag_search 和 search_web 都属于 "search" 组，谁先非错就用谁，另一个被 cancel——本地优先，外网兜底RAG 作为 tool 和普通工具有什么不一样？ 1.闭包捕获 Engine 实例（必须在 application 层注册）2.Loaded 状态前置检查（避免空库浪费 LLM 调用）3.只回 answer 丢弃 results（不污染 prompt）4.跟 search_web 同 race_group 竞速、配套 write_document 形成 self-improving 自反闭环
