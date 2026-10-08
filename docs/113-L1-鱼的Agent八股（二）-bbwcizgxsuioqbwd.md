# 鱼的Agent八股（二）

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/bbwcizgxsuioqbwd
- Slug: bbwcizgxsuioqbwd
- Doc ID: 283993719
- 层级: 1
- 字数: 3336
- 创建时间: 2026-09-04T12:50:01.000Z
- 更新时间: 2026-10-08T02:06:14.000Z
- 发布时间: 2026-09-04T12:50:31.000Z
- 内容更新时间: 2026-09-04T12:50:31.000Z
- 本地同步时间: 2026-10-08T16:25:13+08:00

## 标题结构

- h2: 🤖 为什么选择 Multi-Agent，而不是 Single Agent
- h2: 🔄 Workflow vs Autonomous Agent 怎么取舍
- h2: 📡 Agent 间怎么通信
- h2: 🧹 怎么避免上下文污染
- h2: 🧠 长短期 Memory 怎么设计
- h2: ⚔️ 记忆冲突怎么处理
- h2: 🔍 Memory Retrieval 怎么优化
- h2: 🗜️ 怎么做记忆压缩
- h2: 📊 Agent 评测体系怎么设计
- h2: 📏 Memory Benchmark 怎么设计
- h2: 📈 评测指标主要看哪些
- h2: 🔎 怎么定位 Badcase
- h2: 🌀 怎么降低 Hallucination
- h2: 🎯 什么时候做 SFT
- h2: 🏆 什么时候适合 RL
- h2: ⚡ KV Cache 碎片化怎么解决
- h2: 🔄 Continuous Batching 原理
- h2: 🏗️ 大型 Agent 系统整体分层设计
- h2: 🤝 Sub-Agent 怎么协作
- h2: 🔌 Skill 和 MCP 是什么关系
- h2: 🔍 怎么看 Claude Code 的 Memory 设计
- h2: 💻 手撕代码：最长子串（每字符出现次数 ≥ k）
- h3: 解法一：分治法（推荐面试先说）
- h3: 解法二：滑动窗口（枚举字符种类数，O(N)）

## 资源链接


## 正文

<a id="NxKo2"></a>
## 🤖 为什么选择 Multi-Agent，而不是 Single Agent



**选 Multi-Agent 的理由：**



- **分工专业化**：不同任务交给专门的 Agent，避免单一 Agent 能力稀释

- **并行处理**：多任务并发执行，降低延迟

- **上下文隔离**：每个 Agent 维护独立上下文，避免污染

- **容错性**：单个 Agent 失败不影响全局

- **可扩展性**：新增能力只需新增 Agent，不破坏现有结构




**什么时候坚持 Single Agent：**



- 任务简单、无需并行

- 强依赖全局上下文、拆分会导致信息损失

- 延迟极敏感（多跳通信开销不可接受）




---



<a id="BcT9G"></a>
## 🔄 Workflow vs Autonomous Agent 怎么取舍



| 维度 | Workflow | Autonomous Agent |
| --- | --- | --- |
| 控制流 | 固定/预定义 | 动态决策 |
| 可预测性 | 高 | 低 |
| 灵活性 | 低 | 高 |
| 适用场景 | SOP 明确的流程 | 开放探索型任务 |
| 调试难度 | 容易 | 难 |



**取舍原则**：先用 Workflow 锁定主干，在决策点引入 Autonomous；生产环境优先 Workflow，保稳定性。



---



<a id="QKBGk"></a>
## 📡 Agent 间怎么通信



**三种模式：**



1. **共享内存（Blackboard）**：Agent 读写共享状态库，适合松耦合

2. **消息队列（Message Passing）**：点对点或 pub/sub，异步解耦

3. **直接调用（Orchestrator 模式）**：主 Agent 调度子 Agent，适合层级结构




**实践要点：**



- 通信格式统一（JSON Schema / TypedDict）

- 加版本号，防接口漂移

- 关键消息加幂等 ID，防重复处理




---



<a id="Wathj"></a>
## 🧹 怎么避免上下文污染



- **上下文隔离**：子 Agent 只传入必要 context，不传原始完整对话

- **结果摘要化**：子 Agent 返回 summary 而非 raw output

- **Context Window 管理**：超长上下文做截断+压缩，保留最近 N 轮 + 关键摘要

- **角色分离**：不同 Agent 有独立 system prompt，防止角色混淆

- **注入防护**：对用户输入做 sanitize，防 prompt injection 污染




---



<a id="iRh6E"></a>
## 🧠 长短期 Memory 怎么设计



```
短期 Memory（Working Memory）
├── 当前对话上下文（in-context）
├── 生命周期：单次 session
└── 存储：直接在 context window 中

长期 Memory（Persistent Memory）
├── Episodic（情节记忆）：具体事件/对话历史
├── Semantic（语义记忆）：知识、偏好、用户画像
└── Procedural（过程记忆）：技能、工具使用模式

存储选型：
- 向量数据库（Chroma/Qdrant）→ 语义检索
- KV Store（Redis）        → 快速精确查找
- 关系数据库               → 结构化用户数据
```



---



<a id="p9JBs"></a>
## ⚔️ 记忆冲突怎么处理



**冲突来源**：新信息与旧记忆矛盾（用户改变偏好、事实更新）



**处理策略：**



1. **时间优先**：新记忆权重 > 旧记忆（加时间戳衰减）

2. **置信度加权**：高可信来源覆盖低可信

3. **显式标记**：冲突记忆保留双版本，标注 `[deprecated]`

4. **用户确认**：检测到冲突时询问用户

5. **版本链**：记忆变更走 event sourcing，可回溯




---



<a id="aXk8a"></a>
## 🔍 Memory Retrieval 怎么优化



**检索质量：**



- **稀疏+稠密混合检索**（BM25 + embedding）

- **重排序（Reranker）**：Cross-encoder 精排

- **HyDE**：用假设性答案生成查询向量，提升语义匹配




**检索效率：**



- HNSW 索引（近似最近邻）

- 分层索引：先粗检索，再精排

- 缓存高频 query 结果




**相关性过滤：**



- 时间衰减权重

- 用户 session 隔离

- 相似度阈值截断（避免召回噪声）




---



<a id="IJfsr"></a>
## 🗜️ 怎么做记忆压缩



1. **摘要压缩**：定期用 LLM 对历史对话做 abstractive summary

2. **重要性过滤**：基于访问频率/情感强度保留关键记忆

3. **层级压缩**：




- L1：原始对话

- L2：日摘要

- L3：周/月主题摘要




1. **遗忘曲线**：基于 Ebbinghaus 模型，低访问记忆自动降权/删除

2. **实体提取**：从对话中提取结构化实体（人名、偏好、事实），压缩存储




---



<a id="jMWRW"></a>
## 📊 Agent 评测体系怎么设计



**三层评测：**



```
1. 单步评测
   - 工具调用准确率
   - 指令跟随率
   - 幻觉率

2. 轨迹评测（Trajectory）
   - 任务完成率（Task Success Rate）
   - 步骤效率（最短路径 vs 实际路径）
   - 回溯次数

3. 端到端评测
   - 用户满意度
   - 业务指标（转化率、解决率）
```



**评测方式：**



- **LLM-as-Judge**：用强模型评弱模型输出

- **人工标注**：复杂任务的 gold label

- **沙箱环境**：可执行任务自动验证结果




---



<a id="ifSk5"></a>
## 📏 Memory Benchmark 怎么设计



| 维度 | 测试内容 |
| --- | --- |
| **准确性** | 给定问题，能否检索到正确记忆 |
| **时效性** | 记忆更新后，旧信息是否被正确替换 |
| **长距离** | 跨多轮对话的信息是否能被召回 |
| **冲突处理** | 矛盾信息下的行为是否符合预期 |
| **隐私隔离** | 用户A的记忆不泄漏给用户B |
| **效率** | 检索延迟、存储开销 |



**参考数据集**：MemoryBench、LOCOMO、LongMemEval



---



<a id="C2W0E"></a>
## 📈 评测指标主要看哪些



- **Task Success Rate (TSR)**：端到端任务完成率，最核心

- **Step Accuracy**：每步工具调用/决策正确率

- **Hallucination Rate**：不实信息比例

- **Recall@K**：记忆检索召回率

- **Latency P50/P99**：响应延迟分布

- **Token Efficiency**：完成任务消耗 token 数

- **Grounding Rate**：回答有依据（来源可追溯）的比例




---



<a id="alQtW"></a>
## 🔎 怎么定位 Badcase



**系统化流程：**



1. **日志全链路追踪**：记录每步输入输出，带 trace_id

2. **失败分类**：




- 工具调用失败？

- 规划错误？

- 记忆检索失败？

- LLM 幻觉？




1. **归因分析**：Failure Mode 分层（模型层 / 工具层 / 数据层）

2. **自动聚类**：embedding 相似 badcase，批量发现规律

3. **Replay 工具**：固定随机种子，重放 badcase 便于复现




---



<a id="O2gyu"></a>
## 🌀 怎么降低 Hallucination



- **RAG 增强**：检索事实依据，锚定回答

- **Chain-of-Thought**：显式推理过程，暴露错误链路

- **Self-consistency**：多次采样取多数

- **工具验证**：让 Agent 调用工具验证自身声明

- **置信度输出**：不确定时显式说"不知道"

- **SFT 训练**：在高质量有依据数据上微调，减少凭空生成

- **RLHF/RLAIF**：对幻觉输出给负反馈




---



<a id="neL4G"></a>
## 🎯 什么时候做 SFT



**适合 SFT 的场景：**



- 有大量**高质量标注数据**（>1k 样本）

- 任务**格式固定**（JSON 输出、特定 schema）

- 需要注入**领域知识**（垂直行业）

- Base model 指令跟随差，需要"启蒙"




**不适合 SFT：**



- 数据少、任务变化快、需要强推理




---



<a id="f1y7W"></a>
## 🏆 什么时候适合 RL



**适合 RL 的场景：**



- 有**可验证的奖励信号**（代码能否运行、答案是否正确）

- 任务需要**长程规划**（多步决策）

- SFT 已到瓶颈，需要突破




**典型场景**：代码生成（执行反馈）、数学推理（验证器）、游戏 Agent



**难点**：reward hacking、训练不稳定、采样效率低



---



<a id="Z6bPq"></a>
## ⚡ KV Cache 碎片化怎么解决



**问题根因**：不同序列长度导致显存分配不连续，利用率下降



**解决方案：**



- **PagedAttention（vLLM）**：类似操作系统分页，KV cache 按 page 管理，非连续物理内存通过虚拟地址映射，碎片率接近 0

- **Prefix Caching**：相同前缀（system prompt）共享 KV cache，避免重复计算

- **动态显存分配**：按需申请，避免预分配过大块




---



<a id="muAiz"></a>
## 🔄 Continuous Batching 原理



**传统 Static Batching 问题**：batch 中最长序列结束前，其他序列槽位浪费



**Continuous Batching（Iteration-level Batching）原理：**



```
每个 decode step 后：
1. 检查哪些序列已生成 <EOS>
2. 立即将该槽位替换为等待队列中的新请求
3. 不等整个 batch 完成

效果：GPU 利用率从 ~30% → ~80%+
```



实现关键：scheduler 在每个 token 生成后做调度决策，而非 batch 粒度。



---



<a id="h1zJj"></a>
## 🏗️ 大型 Agent 系统整体分层设计



```
┌─────────────────────────────────┐
│     交互层（Interface Layer）    │  ← 用户/API 入口
├─────────────────────────────────┤
│    编排层（Orchestration Layer） │  ← 主 Agent / Planner
├─────────────────────────────────┤
│    执行层（Execution Layer）     │  ← Sub-Agents / Workers
├─────────────────────────────────┤
│    工具层（Tool Layer）          │  ← MCP / Skills / APIs
├─────────────────────────────────┤
│    记忆层（Memory Layer）        │  ← 向量库 / KV / DB
├─────────────────────────────────┤
│    基础设施层（Infra Layer）     │  ← LLM 推理 / 监控 / 日志
└─────────────────────────────────┘
```



---



<a id="mP4qV"></a>
## 🤝 Sub-Agent 怎么协作



**三种协作模式：**



1. **层级（Hierarchical）**：Orchestrator → Workers，主从关系

2. **流水线（Pipeline）**：A → B → C，顺序传递结果

3. **并行+聚合（MapReduce）**：多 Agent 并发，结果 merge




**协作要点：**



- 定义清晰的输入输出 Schema

- 任务带 deadline 和 priority

- 结果带 confidence score

- 失败有降级策略（fallback）




---



<a id="m36sJ"></a>
## 🔌 Skill 和 MCP 是什么关系



|  | Skill | MCP (Model Context Protocol) |
| --- | --- | --- |
| 定义 | Agent 内部封装的能力单元 | Anthropic 提出的标准化工具协议 |
| 层次 | 应用层抽象 | 协议/传输层标准 |
| 类比 | 一个功能模块 | USB 接口标准 |



**关系**：Skill 是"做什么"，MCP 是"怎么连接"。可以把 Skill 通过 MCP 协议暴露给 Agent 调用。MCP 解决的是工具发现、调用、权限的**标准化**问题。



---



<a id="Jpfpr"></a>
## 🔍 怎么看 Claude Code 的 Memory 设计



**多层文件记忆架构：**



- `MEMORY.md`：长期核心记忆（curated，手动更新）

- `memory/YYYY-MM-DD.md`：每日原始日志

- `HEARTBEAT.md`：周期性检查清单




**设计亮点：**



- **人工可读**：纯 Markdown，人可以直接查看/编辑，透明可信

- **主动维护**：Agent 在 heartbeat 时主动整理压缩

- **分层隔离**：长期 vs 短期明确分离

- **显式持久化**：不依赖隐式状态，"Write It Down"原则




**局限：**



- 无向量检索，纯文本 grep 效率有限

- 规模上去后检索质量下降

- 多用户场景无天然隔离




---



<a id="JV9wI"></a>
## 💻 手撕代码：最长子串（每字符出现次数 ≥ k）



**题目**：给定字符串 s 和整数 k，求最长子串长度，要求子串中每个字符出现次数都 ≥ k



LeetCode 395 · Longest Substring with At Least K Repeating Characters



<a id="OVO4D"></a>
### 解法一：分治法（推荐面试先说）



**核心思想**：出现次数 < k 的字符**一定不能**出现在答案子串中，以这些字符为分割点递归处理。



```
def longestSubstring(s: str, k: int) -> int:
    if len(s) == 0:
        return 0

    from collections import Counter
    freq = Counter(s)

    for char in freq:
        if freq[char] < k:
            # 以该字符分割，递归求各段最大值
            return max(longestSubstring(part, k)
                       for part in s.split(char))

    # 所有字符都满足条件，整串就是答案
    return len(s)
```



**复杂度：**



- 时间：O(N × 26)，最多分割 26 次，每次 O(N)

- 空间：O(N) 递归栈




---



<a id="O6k7x"></a>
### 解法二：滑动窗口（枚举字符种类数，O(N)）



**核心思想**：枚举窗口内允许的不同字符种类数 t（1~26），对每个 t 用滑动窗口求最优解。



```
def longestSubstring(s: str, k: int) -> int:
    res = 0

    for t in range(1, 27):  # 枚举窗口内不同字符种类数
        from collections import defaultdict
        cnt = defaultdict(int)
        left = 0
        unique = 0      # 窗口内不同字符数
        satisfied = 0   # 满足出现次数 >= k 的字符数

        for right in range(len(s)):
            c = s[right]
            if cnt[c] == 0:
                unique += 1
            cnt[c] += 1
            if cnt[c] == k:
                satisfied += 1

            # 窗口内不同字符数超过 t，收缩左边
            while unique > t:
                lc = s[left]
                if cnt[lc] == k:
                    satisfied -= 1
                cnt[lc] -= 1
                if cnt[lc] == 0:
                    unique -= 1
                left += 1

            # unique == t 且全部满足条件
            if unique == t == satisfied:
                res = max(res, right - left + 1)

    return res
```



**复杂度：**



- 时间：O(26 × N) = O(N)

- 空间：O(1)




---



先讲分治（直觉清晰，易于解释），追问优化时给出滑动窗口的 O(N) 解。两种解法都掌握，展示思维广度。



---
