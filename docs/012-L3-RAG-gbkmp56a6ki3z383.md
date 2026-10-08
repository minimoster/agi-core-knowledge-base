# RAG

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/gbkmp56a6ki3z383
- Slug: gbkmp56a6ki3z383
- Doc ID: 269189860
- 层级: 3
- 字数: 1985
- 创建时间: 05/10/2026 19:00:51
- 更新时间: 07/02/2026 01:06:17
- 发布时间: 07/02/2026 01:06:16
- 内容更新时间: 07/02/2026 01:06:16

## 标题结构

- h1: RAG（检索增强生成）能讲的点：
- h2: RAG 解决什么问题：
- h2: 完整 RAG 流程：
- h3: 数据提取流程
- h2: 3:切片策略：
- h3: Markdown 技术知识库切片策略
- h2: 4:混合检索做法：
- h2: 5:为什么混合检索：
- h3: 为什么不能只靠 Dense
- h3: 为什么不能只靠 BM25
- h3: 混合检索真正解决的问题
- h3: 为什么先召回很多，再 rerank
- h1: 工业界经典思路
- h1: RAG评测

## 资源链接



## 正文

RAG（检索增强生成）能讲的点：RAG 解决什么问题：RAG（Retrieval-Augmented Generation）本质上是：“让大模型先查资料，再回答问题。”主要解决几个核心问题：1. 大模型知识过时LLM 参数是训练时固化的。例如： 公司内部文档  最新代码  用户知识库  实时数据 模型本身不知道。RAG 可以： 动态检索最新知识 （因为你的知识库是可以动态删除和增加的） 不需要重新训练模型（微调成本大，效果像开盲盒，越调越傻逼的可能性很大）完整 RAG 流程：数据提取流程首先清洗对于多种文件的方式是不同的，因为我们的知识库只支持上传md文件，所以清洗也只在md文件的基础上考虑就行。Markdown 清洗核心目标做Markdown 清洗时，不是单纯“删字符”。而是：“保留对检索有价值的信息，去掉会污染 embedding 的噪声。”因为 RAG 的问题很多时候不是模型不够强，而是：1. 删除无意义 Markdown 噪声会清理：例如：这些内容： 不具备语义价值  会污染向量表示  增加 retrieval 噪声 2. 保留标题层级结构不会把 markdown flatten 成纯文本。而是保留：因为：标题层级本身就是语义结构。3. 保留代码块（重点）技术知识库里：所以不会删除：4. 保留列表结构例如：不会简单拼成一行。因为列表本身表示：对检索有帮助。5. 表格结构规范化Markdown table 会转成：或者结构化文本。避免： 表格直接塌陷成乱码  embedding 丢失字段关系 6. 去重（Deduplication）很多知识库：会做去重。否则： retrieval 容易重复召回  context 被冗余内容污染以上六条都可以用python脚本实现固定规则然后md文件会变得更加规范，切片也就更方便更有效3:切片策略：Markdown 技术知识库切片策略1. Header-aware Chunking（按标题层级切分）优先按照：进行切分。因为技术文档天然章节化：topic 更集中retrieval precision 更高不容易语义断裂2. Recursive Split（超长 Section 递归切分）如果某个 section：token 过大内容跨度过广才继续：按段落按列表按代码块递归细切。避免：chunk 过大embedding 被多 topic 污染3. Code-aware Chunking（代码块完整保留）不会从中间截断：会完整保留 code block。因为：函数名API 名错误码都是高价值 retrieval signal。4. Small-overlap Strategy（小重叠策略）仅保留少量 overlap：避免：上下文断裂retrieval 重复召回embedding 冗余5. Neighbor Expansion（邻居扩展）检索命中 chunk 后：自动补充：前一个 chunk后一个 chunk恢复章节连续语义。因为技术文档：topic 通常沿 section 连续分布6. Parent-Child Chunking（父子块检索）区分：Child Chunk小粒度：用于 retrievalembedding 更精准Parent Context大粒度：用于 generation保证上下文完整流程：切完存入向量数据库即可4:混合检索做法：向量召回（稠密向量索引）：召回语义相似的chunk 缺点：容易忽略精确名词，容易走歪关键词召回（倒排索引 + BM25 打分）：召回关键词次数最多的文档chunk缺点：无法理解语义。​我们做法是混合检索5:为什么混合检索：Dense（向量检索）和 BM25（关键词检索）各有盲区。混合检索本质上是在解决：1. 向量检索 的问题Dense 擅长：语义相似同义表达意图理解比如：Query：即使文档里写的是：向量也能召回。因为 embedding 学到了：但 Dense 有几个致命问题：（1）关键词不敏感比如：Dense 可能只理解：但：4o mini128k特定版本号这些精确词可能丢。尤其：API 名类名错误码人名SKU函数名Dense 很容易翻车。（2）容易语义漂移Query：Dense 可能召回：因为 embedding 觉得：但其实不精准。2. BM25 的问题BM25 擅长：精确关键词ID专有名词错误码API 名称比如：BM25 极强。但 BM25 不懂语义。例如：Query：文档：BM25 根本召不回来。因为：所以两者互补能力DenseBM25语义理解强弱精确关键词弱强同义词强弱专有名词弱强长尾错误码弱强自然语言问题强弱所以工业界会：为什么不能只靠 Dense很多人一开始觉得：但实际线上：Dense 经常漏掉：函数名版本号配置项SQL字段API路径报错文本这些在技术 RAG 里极重要。比如：BM25 秒杀 Dense。为什么不能只靠 BM25因为用户根本不会用文档原词提问。用户会说：文档写：只有 Dense 能理解。混合检索真正解决的问题核心是：RAG 最大问题通常不是：而是：LLM 再强：所以工业界非常重视：为什么先召回很多，再 rerank因为：即：后面再：RRFCrossEncoderLLM rerank慢慢过滤。工业界经典思路一般：本质：第一阶段尽可能：第二阶段再：这是现代 RAG 的核心思想。RAG评测标准流程介绍：我们项目真实评测过程及其数据：
