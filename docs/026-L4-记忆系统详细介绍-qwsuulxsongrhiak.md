# 记忆系统详细介绍

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/qwsuulxsongrhiak
- Slug: qwsuulxsongrhiak
- Doc ID: 274279773
- 层级: 4
- 字数: 2961
- 创建时间: 06/17/2026 11:54:43
- 更新时间: 07/07/2026 13:00:03
- 发布时间: 07/02/2026 12:13:03
- 内容更新时间: 07/02/2026 12:13:03

## 标题结构

- h1: 一、整体架构
- h2: 三、五种记忆形态
- h3: 3.1 ShortTerm（短期对话窗口）
- h3: 3.2 Preference（结构化偏好）
- h3: 3.3 LongTerm（长期语义记忆）
- h3: 3.4 GraphMemory（图增强层）
- h3: 3.5 TaskMemBuffer（任务步骤环形缓冲）
- h2: 四、写入链路
- h3: 4.0 长期记忆到底怎么写入
- h3: 4.1 时序图
- h3: 4.2 写入即分类（双通道分类管线）
- h3: 4.3 写入去重（隐式合并）
- h3: 4.4 异步建图
- h2: 五、召回链路
- h3: 5.1 三种召回策略对应三类槽位
- h3: 5.2 召回综合分公式
- h3: 5.3 SlotFilter 声明式过滤
- h3: 5.4 1-hop 图扩展
- h2: 六、合并链路（衰减/去重/合并/过期）
- h3: 6.1 触发器
- h3: 6.2 Phase 1：指数衰减
- h3: 6.3 Phase 2：去重 + 合并（双阈值分流）
- h3: 6.4 Phase 3：双门槛过期淘汰

## 资源链接



## 正文

一、整体架构三、五种记忆形态每种记忆形态对应不同的"使用模式"，不是把所有数据扔一个表。3.1 ShortTerm（短期对话窗口）结构：机制：固定窗口，超 MaxTurns × 2 条丢弃最早记录：3.2 Preference（结构化偏好）结构：双通道写入：规则路（同步、零延迟）：LLM 路（异步、准确）——在 extractMemoryFromReply 里调 LLM 抽 k-v JSON为什么两条通道：单 LLM 路：用户说完"我叫张三"立即问"我叫什么"——LLM 抽取还没完成，第二轮回答会"不知道"单规则路：覆盖率窄，只能命中"我叫/我喜欢/我爱"几种两条并行：规则保证即时一致性，LLM 保证长尾覆盖率3.3 LongTerm（长期语义记忆）结构：核心字段含义：Importance是召回的二级信号（公式 s = sim*0.7 + Importance*0.3 ），随时间衰减Category 是装配阶段的过滤维度——让"用户身份"不会被"昨天聊过的菜谱"挤掉SlotHint 是槽位归属建议——promptctx 装配时按此分流3.4 GraphMemory（图增强层）结构：Neo4j 节点：(:Memory {mem_id, content, importance})边类型：FOLLOWS ：时序相邻（前一条 → 当前）SIMILAR_TO ：写入时实时计算 Cosine ≥ simThresh 自动建关键能力：1-hop 图扩展：召回时不仅返回向量命中条目，还沿边扩 1 跳图中心度合并保护：入度 ≥3 的节点免于淘汰3.5 TaskMemBuffer（任务步骤环形缓冲）结构：生命周期：跟 ReAct 任务绑定。新任务开始时 Reset() 清空，任务步骤执行后 Push() 追加。为什么独立：任务级步骤观察（"调了 weather_api 返回 22℃"）和长期记忆（"用户偏好咖啡"）是两种生命周期完全不同的东西。混存会出现"任务结束后步骤观察污染长期召回"。四、写入链路4.0 长期记忆到底怎么写入长期记忆的写入 = 用户消息或 assistant 回复 → 异步交给 LLM 抽 k-v → 抽到才拼短句 embed → 去重后同时写内存/图/PG 三层。原始输入永远不直接进长期记忆，抽不出 k-v 就等于什么都没发生4.1 时序图4.2 写入即分类（双通道分类管线）入口：规则层：LLM 兜底：调一次 LLM 返回 JSON {category, tags, slot_hint}，失败回落到general。为什么必须分类：没有分类：召回阶段只能用 Top-K，"用户姓名"和"上次讨论的菜谱"按相似度竞争。如果当前问题是"做菜"，姓名信息会被排到 K 之外有分类：身份信息走 FilterByCategory(["identity"]) 不算相似度，episodic 走 RecallByFilter(Categories=["episodic"]) 算相似度——两路互不干扰4.3 写入去重（隐式合并）设计意图：去重不是"丢弃"，而是"加固"——多次提到的事实自然 Importance 累积类别升级遵循"具体战胜泛化"—— general 永远会被更具体的类别覆盖写入期就做去重，避免依赖 Consolidate 的 O(n²) 后处理4.4 异步建图入口：linkSimilarEdges ：扫最近 50 条记忆，对 Cosine ≥ simThresh 的建 SIMILAR_TO 边。为什么异步 + 限 50 条：异步：不让 Neo4j 写入阻塞主链路限 50：避免新记忆插入时全表扫描；时序相邻的记忆相似性最高五、召回链路5.1 三种召回策略对应三类槽位槽位召回方式Profile按 Category 枚举，不算相似度Recall向量 + TF 兜底 + 1-hop 图扩展TaskMemring buffer 取最近 K身份信息走纯枚举——你的名字不会因为这轮问题不相关就被排到 TopK 之外。5.2 召回综合分公式为什么权重这样分：相似度（0.7）是主信号：用户问"我喜欢什么"，必须召回"喜欢咖啡"而不是"喜欢早起"重要性（0.3）是次信号：相同语义下，重要的优先；让指数衰减真正影响召回结果不让 Importance 主导（避免老旧的高 importance 永远霸榜）5.3 SlotFilter 声明式过滤结构：主循环：注意点：MaxAgeHours 是"年龄硬过滤"，按 CreatedAt 截断；Importance 衰减是软信号——两者互补召回时刷新 LastAccessed ——访问触达即"重新激活"，间接保护活跃记忆不被合并/淘汰5.4 1-hop 图扩展GraphMemory.RecallByFilter：调底层 ltm.RecallByFilter 拿 seedexpandMemoryNeighbors(seedIDs, 1) 走 Neo4jCypher：扩展条目固定打 Score = 0.45——能进 prompt 但不会压过强相关命中合并去重 + 排序 + TopK 截断设计意图：图扩展是"主动联想"——用户问"上次的咖啡"，向量召回拿到"喜欢咖啡"，图层补回时序相邻的"昨晚熬夜了"——发现间接关联但不直接相似的历史。六、合并链路（衰减/去重/合并/过期）这是整个记忆系统最复杂、最关键的部分。Consolidate 是四阶段管线。6.1 触发器NeedConsolidation：调用点：为什么计数触发而非定时：低活跃期不空转高活跃期及时清理，防止重复条目堆积异步执行不阻塞用户响应6.2 Phase 1：指数衰减代码：关键设计点：按 CreatedAt 计算而非"上次衰减时间"——幂等，重复跑不会累积错误0.995^days 是日衰减系数——30 天 ≈ 86%，100 天 ≈ 61%Δ ≥ 0.01 才入 DecayUpdates ——控制 PG 写放大；变化太小不值得 UPDATE6.3 Phase 2：去重 + 合并（双阈值分流）代码：mergeItems 细节：主体：Importance 高的为 baseImportance ：取 maxContent ：子串关系取长，否则 ； 拼接Embedding ：按 Importance 加权平均LastAccessed ：刷新到 now为什么合并不调 LLM：确定性、低延迟、可单测LLM 改写在大规模下成本爆炸缺点：长期演进会出现"用户偏好咖啡；用户喜欢拿铁"的累积——可演进为 LLM rewriter6.4 Phase 3：双门槛过期淘汰代码：双门槛 AND 关系：必须同时 days > TTLDays(30) AND Importance < MinImportance(0.3) 才删"老但仍重要"被永久保留——这是真正的设计意图TTL 不是单方面"到期就删"
