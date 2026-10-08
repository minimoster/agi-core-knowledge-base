# 🐟的Agent开发学习路线

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/ygcuieb0br2lt4wp
- Slug: ygcuieb0br2lt4wp
- Doc ID: 273181355
- 层级: 2
- 字数: 3582
- 创建时间: 06/09/2026 07:13:04
- 更新时间: 07/04/2026 10:01:41
- 发布时间: 06/09/2026 07:13:18
- 内容更新时间: 06/09/2026 07:13:18

## 标题结构

- h3: 作者背景
- h2: 第一部分：核心知识与必备基础 （大模型基础八股）
- h3: 1.1 大模型与 Agent 基础概念
- h3: 1.2 Agent 系统基本架构
- h2: 第二部分：主流的开发框架
- h3: 2.1 主流 Python Agent 框架对比
- h2: 第三部分：RAG - 让 Agent 拥有你的知识（很重要，必不可少）
- h3: 3.1 RAG 核心流程
- h3: 3.2 RAG 技术栈拆解
- h1: 第四部分：多智能体，React，A2A，ToT，CoT这些概念
- h2: 一、ReAct（Reason + Act）
- h3: 📌 核心论文
- h3: 它解决了什么问题？
- h3: 二、多智能体**（Multi-Agent）**📚 推荐论文
- h3: 🎯 多智能体解决的问题
- h3: 🧱 常见多 Agent 架构
- h1: 第五部分：上下文工程
- h2: 一、MemGPT
- h2: 二、mem0（工程化实现）（仿造人脑海马体的记忆框架）
- h2: 三、向量数据库与记忆
- h2: 四、上下文工程的核心能力
- h1: 第五部分：评测、安全、监控与链路追踪
- h3: 4.1 评测与可观测性 (Evaluation & Observability)
- h1: 第六部分：进阶内容：Lora微调，RL强化学习，BERT模型，Planner优化

## 资源链接

- a: https://github.com/Sea-Go/Sea-BreakTheWaves,https://github.com/Sea-Go/Sea-RideTheWind https://github.com/Sea-Go/Sea-BreakTheWaves,https://github.com/Sea-Go/Sea-RideTheWind
- a: https://www.promptingguide.ai/zh/techniques/tot https://www.promptingguide.ai/zh/techniques/tot
- a: https://jalammar.github.io/illustrated-transformer/ 图解 Transformer (The Illustrated Transformer)
- a: http://arthurchiao.art/blog/visual-intro-to-transformers-zh/ 什么是 GPT？Transformer 工作原理的动画展示
- a: https://python.langchain.com/v0.2/docs/introduction/ LangChain
- a: https://langchain-ai.github.io/langgraph/ LangGraph
- a: https://docs.llamaindex.ai/en/stable/ LlamaIndex
- a: https://microsoft.github.io/autogen/ AutoGen
- a: https://www.notion.so/RAG-31917df30269805b9b16ea5c4e17a67a?pvs=21 https://www.notion.so/RAG-31917df30269805b9b16ea5c4e17a67a?source=copy_link
- a: https://www.pinecone.io/learn/vector-embeddings-for-developers/ 什么是 Embedding？
- a: https://arxiv.org/abs/2210.03629 https://arxiv.org/abs/2210.03629
- a: https://arxiv.org/abs/2303.17760 https://arxiv.org/abs/2303.17760
- a: https://github.com/microsoft/autogen https://github.com/microsoft/autogen
- a: https://arxiv.org/abs/2310.08560 https://arxiv.org/abs/2310.08560
- a: https://github.com/mem0ai/mem0 https://github.com/mem0ai/mem0
- a: https://www.langchain.com/langsmith 官网
- a: https://www.trulens.org/ 官网
- a: https://phoenix.arize.com/ 官网
- a: https://github.com/confident-ai/deepeval DeepEval
- a: https://github.com/openai/evals OpenAI Evals

## 正文

文档首先路线基于AI生成然后我针对自己的学习经历，路线做了一些相关优化，主要还是对我自己的背景下学习路线的整理（后端转Agent开发经历），我认为目前Agent开发还是很新型的岗位，不像后端没有沉淀很久，成型的八股，面试会问的也是基础和目前相关的热点（mcp，skil等等），我相信未来AI的应用场景会越来越多，与其恐惧ai带来的传统革命，不如成为AI的创造者和使用者食用注意：避免篇幅过长，这里主要是过一遍Agent开发需要学习的名词，不会做深度解析，可以通过本文了解需要学习的大概内容，避免迷茫 💡 这里打个广告，我想和志同道合的人一起维护一个开源Agent项目，可以是垂直领域的业务Agent，有一定应用价值。也可以是类似于Go语言复写优化mem0，或者Agent各种能力  的优化的想法，或者其他奇思妙想idea都可以，大家欢迎交流！！ 这是一个社区——识海，如果大家也有同样的想法，想参与别人的项目，或者有项目想邀请人一起参与，可以加qq群聊一起交流一下，欢迎：750807478 推荐一下我们小伙伴做的的项目：https://github.com/Sea-Go/Sea-BreakTheWaves,https://github.com/Sea-Go/Sea-RideTheWind作者背景博主本身是27届，大二暑期开始实习，从Go后端开发入门Agent开发，有两段后端实习基础，在第二段实习有机会接触了Agent开发相关的内容，加上在学校实验室做过一些RAG，大模型的研究（人工智能专业），一下经历是我这个背景下的思考下面是我 一月份 面试过的大厂，中厂Agent开发相关岗位，列这些是想说明我有切身面过很多Agent相关岗位，比较了解面试官想要什么，Agent相关的面试模式：快手Agent开发（二面挂）快手c端广告 java + Agent开发（offer）minimax Agent开发（offer）字节跳动懂车帝 Agent架构开发（offer）商汤 人才计划AI Agent开发（offer）腾讯提前批HR线 AI全栈（三面挂），这个本身就不太匹配，事业线也不是很好，算是练手，技术栈java+py第一部分：核心知识与必备基础 （大模型基础八股） 💡 如果你是后端，想补一点agent相关的知识，那Transformer架构，GPT架构，机器学习深度学习的基础不是你必须的，但是你最好了解现在比较火的mcp，skil，functioncal等概念 1.1 大模型与 Agent 基础概念https://www.promptingguide.ai/zh/techniques/tot这里是大模型的基础知识，重点包括Transformer架构，GPT架构，机器学习深度学习的基础Transformer 架构：一切的基石。现代 LLM（如 GPT 系列）都基于此架构。你不需要从零实现它，但理解其核心的 自注意力机制 (Self-Attention) 如何让模型理解上下文至关重要。精选资料：图解 Transformer (The Illustrated Transformer) (英文，强烈推荐)：这篇博客用通俗易懂的图文形式解释了 Transformer 的工作原理，是入门的黄金标准。什么是 GPT？Transformer 工作原理的动画展示 (中文翻译)：3Blue1Brown 的视频译文，用动画直观展示了数据在 Transformer 内部的流动过程。开山鼻祖级别的存在，如果想做agent开发一定要有印象，大模型各种应用基本离不开TransformerLLM 推理与调用：我们通常通过 API 来使用大模型。你需要了解如何调用 OpenAI、Anthropic (Claude) 或国产大模型的 API，并理解 Token、温度 (Temperature)、Top-p 等核心参数的含义温度，top-p这些参数需要了解，八股会问！！！提示工程 (Prompt Engineering)：与 LLM 沟通的艺术。好的提示能极大提升模型表现。你需要掌握一些基本原则，如提供清晰指令、上下文、示例（Few-shot Learning）等。编写prompt的能力，结合Skill相关概念学习函数调用/工具使用 (Function Calling / Tool Use)：这是 Agent 的“手和脚”。LLM 本身无法与外部世界交互，但通过函数调用，它可以“请求”我们执行代码（如查询数据库、调用 API、搜索网页），从而获取外部信息或执行操作。Function Calling部分现在常用MCP，需要了解MCP和fc的区别，这个经常问！！！1.2 Agent 系统基本架构一个Agent 系统通常包含以下几个部分，理解这个流程至关重要。第二部分：主流的开发框架学习目标：工欲善其事，必先利其器。第二周我们将聚焦于当前最主流的 Agent 开发框架，学习如何使用它们快速搭建 Agent 应用，并理解各自的优劣势。最佳实践：对于初学者，强烈建议从 LangChain开始。拥有最庞大的社区和最丰富的文档资源。掌握其中一个，再触类旁通其他框架会容易得多。 💡 如果你会python 先学LangChain就好了，其次是掌握Agent评测——langsimith 如果你想学基于Go的agent开发，可以学习字节开源的Eino框架 2.1 主流 Python Agent 框架对比框架核心优势适用场景上手难度官方链接LangChain & LangGraph大而全的“瑞士军刀”，组件化程度高，生态最成熟，支持几乎所有主流模型和工具。LangGraph 提供了更灵活的图结构来构建复杂 Agent。快速原型验证、构建复杂的 Agent 工作流、多工具协作。★★☆☆☆ (概念较多)LangChain / LangGraphLlamaIndex专注 RAG，数据索引和检索能力极强，尤其擅长处理复杂文档（如 PDF、PPT），与 LangChain 无缝集成。构建知识库问答、文档分析、数据驱动的 Agent。★★☆☆☆LlamaIndexAutoGen (Microsoft)多 Agent 协作框架，擅长通过定义不同角色的 Agent 并让它们对话来解决复杂问题。模拟团队协作（如“产品经理+程序员+测试”小组）、代码生成与调试、复杂规划任务。★★★☆☆AutoGen第三部分：RAG - 让 Agent 拥有你的知识（很重要，必不可少）可以参考这里https://www.notion.so/RAG-31917df30269805b9b16ea5c4e17a67a?source=copy_link学习目标：掌握检索增强生成（Retrieval-Augmented Generation, RAG）的核心技术栈。这是让 Agent 能够基于私有知识（如公司内部文档、个人笔记）进行回答的关键，也是目前商业应用最广泛的场景。 💡 其实Rag这块如果深入学习，干货也非常多，各个场景适用的Rag优化策略不同，Rag种类也很多，并且如果是工业界实际落地，也需要针对数据类型，效果等深入完善系统，这里只做简单罗列，在后面视频会深入解析Rag整个流程和优化 3.1 RAG 核心流程RAG 的本质是“先检索，再生成”。当用户提问时，系统首先从知识库中找到最相关的文档片段，然后将这些片段和用户问题一起作为上下文，交给 LLM 生成最终答案。3.2 RAG 技术栈拆解文档加载与切分 (Loading & Splitting)加载：首先需要从各种来源（PDF, TXT, HTML, Notion, ...）加载文档。切分：将长文档切分成更小的、语义完整的块 (Chunks)。切分的好坏直接影响检索效果。常见的策略有按字符数切分、按 Token 数切分、递归字符切分等。主流工具：LangChain 和 LlamaIndex 都提供了丰富的 DocumentLoaders 和 TextSplitters。嵌入与向量存储 (Embedding & Vector Stores)嵌入 (Embedding)：使用 Embedding 模型将文本块转换为高维向量。这些向量能够捕捉文本的语义信息，相似的文本在向量空间中距离更近。向量数据库 (Vector Database)：专门用于存储和高效检索这些高维向量的数据库。精选资料：什么是 Embedding？ (英文，Pinecone 出品)第四部分：多智能体，React，A2A，ToT，CoT这些概念 💡 实际应用的业务Agent架构都是相当复杂的，这里需要去学习更深入的Agent技术，包括A2A的具体实现，是什么，React的具体实现，解决了什么问题，多智能体架构是什么，怎么多协同，这个阶段我还是推荐通过一个项目去学习 一、ReAct（Reason + Act）📌 核心论文ReAct: Synergizing Reasoning and Acting in Language Models  https://arxiv.org/abs/2210.03629这是所有 Agent 框架的祖师爷级论文。它解决了什么问题？在 ReAct 之前：要么纯 CoT（只推理，不查资料）要么工具调用（直接调用工具，没有显式推理链）ReAct 做了一件事：模型：先思考再决定是否调用工具再根据结果继续思考这让 Agent：能动态查资料能纠错能自我反思二、多智能体**（Multi-Agent）**📚 推荐论文CAMEL: Communicative Agents for Mind Exploration https://arxiv.org/abs/2303.17760AutoGen（微软） https://github.com/microsoft/autogenAutoGen 是目前最有代表性的多智能体框架。🎯 多智能体解决的问题单 Agent 问题多 Agent 解决方式上下文爆炸角色拆分推理能力弱专家化复杂流程难控流程分层🧱 常见多 Agent 架构1️⃣ Supervisor 模式2️⃣ Debate 模式3️⃣ loop 模式（推荐你重点学）👉 看：LangGraphAutoGen GroupChat第五部分：上下文工程Agent 的记忆与状态：为了完成复杂任务，Agent 需要记住之前的对话历史（短期记忆）和关键信息（长期记忆）。我们通常使用简单的缓冲区或更高级的向量数据库来实现。本质就是上下文工程，这个可以看一下Memgpt这篇论文，其次是我后面会出的mem0架构的讲解，这里不细讲一、MemGPT论文：MemGPT: Towards LLMs as Operating Systemshttps://arxiv.org/abs/2310.08560它的核心思想：把 LLM 当操作系统内存分成：Working Memory（上下文窗口）Archival Memory（外部存储）Recall 机制Memory Paging像极了操作系统的：这篇论文会改变你对 Agent 的理解。二、mem0（工程化实现）（仿造人脑海马体的记忆框架）mem0 的核心思想是：自动提取重要记忆，而不是存全部历史区别于传统 buffer memory：只存“重要事实”自动更新可长期使用推荐你看：https://github.com/mem0ai/mem0三、向量数据库与记忆学习方向：Embedding 原理Top-K 检索Hybrid Search（BM25 + Dense）父子索引四、上下文工程的核心能力真正高级的 Context Engineering 包括：Prompt 编排动态裁剪Memory 压缩Summary 机制角色化上下文隔离Token 预算调度第五部分：评测、安全、监控与链路追踪4.1 评测与可观测性 (Evaluation & Observability)“没有度量，就没有优化。” 如何客观地评估 Agent 的表现，是其能否上线的核心问题。核心评测维度：RAG 质量：上下文精度 (Context Precision)、上下文召回率 (Context Recall)、答案忠实度 (Faithfulness)、答案相关性 (Answer Relevancy)。Agent 质量：任务完成率、工具使用正确率、鲁棒性等。评测方法：人工评估：最可靠，但成本高。LLM-as-a-Judge：使用更强大的 LLM（如 GPT-4）来评估 Agent 的输出，是目前的主流方案。工具/框架核心特点亮点功能官方链接LangSmithLangChain 官方出品，集日志、监控、调试、评估于一体的平台。与 LangChain/LangGraph 无缝集成。可视化追踪 Agent 每一步的思考和工具调用，轻松调试；支持在线和离线评估，自定义评估器。官网TruLens专注于 RAG 的 “三元组” 评估（答案、上下文、问题之间的相关性），提供细粒度的可观测性。开箱即用的 RAG 评估指标，可视化展示检索与生成环节的问题。官网Phoenix (Arize)基于 OpenTelemetry 的开源 LLM 可观测性工具，支持日志、评估、可视化，与各种框架集成良好。强大的可视化能力，能通过 Embedding 聚类发现问题数据。官网DeepEval / OpenAI Evals更偏向于单元测试和基准测试的评估框架，让你像写 Pytest 一样编写 LLM 的评测用例。与 CI/CD 流程集成，自动化回归测试。DeepEval / OpenAI Evals第六部分：进阶内容：Lora微调，RL强化学习，BERT模型，Planner优化
