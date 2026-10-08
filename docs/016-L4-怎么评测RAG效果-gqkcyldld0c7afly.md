# 怎么评测RAG效果

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/gqkcyldld0c7afly
- Slug: gqkcyldld0c7afly
- Doc ID: 269664836
- 层级: 4
- 字数: 739
- 创建时间: 05/13/2026 14:43:58
- 更新时间: 06/28/2026 11:11:18
- 发布时间: 06/26/2026 08:51:37
- 内容更新时间: 06/26/2026 08:51:37

## 标题结构

- h1: 省流：
- h1: 一、先理解：RAG 最容易坏在哪
- h1: 二、Retrieval 怎么测（最重要）
- h1: 1. 准备评测集（黄金数据）
- h1: 2. 测 Recall@K
- h1: 举例
- h1: 为什么 Recall 最重要
- h1: 3. 测 MRR（Mean Reciprocal Rank）
- h1: 举例
- h1: 4. 测 NDCG（高级一点）
- h1: 三、Generation 怎么测
- h1: 1. Faithfulness（是否胡编）
- h1: 2. Answer Relevance
- h1: 3. Context Precision
- h1: 四、工业界最常用评测框架
- h1: 1. RAGAS
- h1: 2. DeepEval
- h1: 3. LangSmith
- h1: 五、最标准的 RAG Benchmark 流程
- h1: 六、一个非常重要的现实

## 资源链接



## 正文

省流：测这些RAG 评测其实分四层：工业界最重要的是：因为很多问题根本不是 LLM 的锅。一、先理解：RAG 最容易坏在哪通常：模块常见问题Chunk切太短/太长Embedding语义表达差Recall没召回来BM25关键词不准Rerank排序错Context Assembly上下文拼接乱Generation幻觉但实际上：最大问题通常是 Recall 不够。即：二、Retrieval 怎么测（最重要）1. 准备评测集（黄金数据）你需要：例如：Question正确ChunkRedis 为什么会缓存雪崩“缓存雪崩章节”JWT 为什么无状态“JWT 原理”Kafka 如何保证顺序消费“Partition 顺序机制”这个叫：2. 测 Recall@K最核心指标。意思：举例100个问题：83个问题正确chunk出现在Top5那么：为什么 Recall 最重要因为：所以：3. 测 MRR（Mean Reciprocal Rank）看：举例正确答案：第1名 → 1第2名 → 0.5第5名 → 0.2平均后：4. 测 NDCG（高级一点）看：适合：工业界常用。三、Generation 怎么测即：1. Faithfulness（是否胡编）最关键。看：不是：2. Answer Relevance回答是否真正回答了问题。3. Context Precision给的context是否有用。避免：四、工业界最常用评测框架现在最主流：1. RAGAS最火。可测：指标含义faithfulness是否忠于contextanswer_relevancy回答相关性context_precisioncontext质量context_recall是否召回正确context非常适合：2. DeepEval更工程化。适合：CI/CD自动化测试Agent评测3. LangSmith适合：trace调试可视化五、最标准的 RAG Benchmark 流程工业界通常：六、一个非常重要的现实很多人：这是错误的。因为：你根本不知道：是 retrieval 错rerank 错prompt 错还是 LLM 错所以：RAG 一定要“分层评测”。否则根本无法优化。
