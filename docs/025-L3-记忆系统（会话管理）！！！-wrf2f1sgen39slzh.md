# 记忆系统（会话管理）！！！

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/wrf2f1sgen39slzh
- Slug: wrf2f1sgen39slzh
- Doc ID: 269189854
- 层级: 3
- 字数: 489
- 创建时间: 05/10/2026 19:00:45
- 更新时间: 07/07/2026 06:10:46
- 发布时间: 07/07/2026 06:10:45
- 内容更新时间: 07/07/2026 06:10:45

## 标题结构

- h1: 引言
- h1: 业界承认度最高的两个方案
- h1: 我们教学项目的具体实现
- h2: 1:用 Mem0 思想解决：
- h2: 2:用 Viking 思想解决：
- h1: 实现方案
- h2: 1:去重
- h2: 2:合并
- h3: Step1：召回相似记忆
- h3: Step2：相似度判断
- h3: Step3：LLM 合并
- h3: Step4：更新 Memory
- h2: 3:过期和重要性

## 资源链接



## 正文

引言笔者认为在未来最牛逼的agent技术部分绝对是围绕记忆系统去建设的，所以此章内容吃透绝对是无与伦比的提升！科普文章：业界承认度最高的两个方案1:mem0框架2:字节的viking框架个人观点：我们教学项目的具体实现1:用 Mem0 思想解决：fact extractiondedupentity graphsemantic retrieval2:用 Viking 思想解决：runtime stateplanner statetask memorytool statecontext assembly实现方案短期记忆：滑动窗口维持最近的多轮对话摘要长期记忆：存入milvs和es，混合检索，保证精确性和语义性，对长期记忆进行纬度维护。长期记忆管理：去重、合并、过期、重要性衰减机制（模仿mem0 有 memory consolidation（相似记忆自动合并）和去重机制）使用记忆的时候怎么组织上下文呢？（借鉴viking思想）：1:去重双重去重：（硬去重）哈希去重+（软去重）向量化去重向量化去重：本条记忆如果与长期记忆表中检索到一条相似度大于0.92的，即放弃存入长期记忆表2:合并Step1：召回相似记忆Step2：相似度判断例如：进入 consolidation。Step3：LLM 合并Prompt：输出：Step4：更新 Memory不是新增。而是：3:过期和重要性ttl和importance本质是分不开的ttl随着importance的动态变化而变化
