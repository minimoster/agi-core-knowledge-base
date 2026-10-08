# mem0 🆚 viking

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/uy509v64cse72mgk
- Slug: uy509v64cse72mgk
- Doc ID: 269189936
- 层级: 4
- 字数: 1349
- 创建时间: 2026-05-10T19:05:00.000Z
- 更新时间: 2026-10-03T10:19:43.000Z
- 发布时间: 2026-10-03T10:19:42.000Z
- 内容更新时间: 2026-10-03T10:19:42.000Z
- 本地同步时间: 2026-10-08T16:25:13+08:00

## 标题结构

- h1: 两种不同的 AI Memory 思想流派
- h1: 一、Mem0 的核心思想
- h1: 二、Mem0 的架构其实很高级
- h2: ADD-only Memory
- h2: Multi-Signal Retrieval
- h2: Async Memory Write
- h1: 三、Mem0 最大优点
- h2: 工程解耦做得非常好
- h2: 很适合作为中间件
- h1: 四、Mem0 最大缺点
- h2: 本质是“外挂 Memory”
- h2: 记忆只是“检索”
- h1: 五、Viking 的思想其实更先进
- h1: 六、两者最本质的区别
- h1: Mem0 是“记忆检索”
- h1: Viking 是“上下文编排”
- h1: 七、Viking 的优点
- h2: 更适合 Agent
- h2: 更容易做复杂工作流
- h1: 八、Viking 的缺点
- h2: 耦合重
- h2: 不够通用
- h1: 九、现在行业已经开始分层
- h2: 第一代 Memory
- h2: 第二代 Memory
- h2: 第三代（Mem0）
- h2: 第四代（Viking / Runtime Memory）
- h1: 十、业界现在的方向
- h2: 更接近 Viking
- h1: 十一、笔者的建议
- h2: 用 Mem0 思想解决：怎么存记忆
- h2: 用 Viking 思想解决：怎么组织上下文
- h1: 十二、真正未来的方向

## 资源链接


## 正文

<a id="eb502314"></a>
# 两种不同的 AI Memory 思想流派



虽然最终目标都一样：



```
让 Agent “长期记住东西”
```





但是两者是截然不同的思想和实现方式：



1:中间件思想：Mem0 更偏“Memory Middleware”



2:操作系统运行时：Viking 更偏“Memory-Native AI Runtime”



它们不是简单替代关系。



---



| **对比** | **Mem0** | **Viking** |
| --- | --- | --- |
| 核心定位 | 独立记忆层 | Agent Runtime 内置记忆 |
| 思想 | Memory as Service | Memory as Context Infrastructure |
| 重点 | Memory Retrieval | Context Orchestration |
| 优势 | 开源、通用、易接入 | 工程化强、系统统一 |
| 缺点 | 偏外挂 | 耦合更深 |
| 适合 | 快速集成记忆 | 做完整 Agent OS |
| 本质 | Memory SDK | AI Runtime |



---



<a id="33d88949"></a>
# 一、Mem0 的核心思想



Mem0 最核心的一点：



**“不要存聊天记录，而是存事实”**



这是它真正厉害的地方。



传统 memory：



```
把全部聊天存进去
```



然后：



```
下一次全塞 prompt
```



问题：



- token 爆炸

- latency 爆炸

- 噪声巨大




Mem0 的思路：



**从对话中“蒸馏”记忆**



例如：



用户说：



```
我喜欢 Go
我在东京
我在做 Agent
```



Mem0 不会存整个对话。



而是提取：



```
用户喜欢 Go
用户在东京
用户正在开发 AI Agent
```



这叫：



**Fact-based Memory（基于事实的记忆）**



这是 Mem0 的核心创新。 (Mem0)



---



<a id="a4c4314c"></a>
# 二、Mem0 的架构其实很高级



它本质上：



已经不是 Vector DB 了



而是：



“Memory Pipeline”



包括：



```
Conversation
    ↓
Memory Extraction
    ↓
Deduplication
    ↓
Entity Linking
    ↓
Memory Store
    ↓
Hybrid Retrieval
```



里面有几个关键点：



---



<a id="mwHDh"></a>
## ADD-only Memory



Mem0 很重要的设计：



**不覆盖旧记忆**



例如：



```
2025：用户喜欢 Java
2026：用户喜欢 Go
```



它不会删。



而是：



```
同时存在
```



因为：



**记忆是时间态的**



笔者这个设计非常聪明且有道理。 (Mem0)



---



<a id="Xa4AD"></a>
## Multi-Signal Retrieval



Mem0 不只是向量搜索。



而是：



```
Semantic
+
Keyword(BM25)
+
Entity Graph
```



联合召回。 (Mem0)



这是很多简单 memory 系统做不到的。



---



<a id="async-memory-write"></a>
## Async Memory Write



Mem0 后面发现：



**Memory 写入不能阻塞响应**



所以：



```
用户响应先返回
Memory 后台异步写
```



这是很典型的生产级优化。 (Agent Market Cap)



---



<a id="bc98308a"></a>
# 三、Mem0 最大优点



<a id="TY7ZU"></a>
## 工程解耦做得非常好



它不像 LangGraph 那种：



```
必须绑定 runtime
```



Mem0：



```
memory.add()
memory.search()
```



就能接。



所以：



<a id="XRG0A"></a>
## 很适合作为中间件



这也是为什么：



现在很多 Agent：



- CrewAI

- AutoGen

- LangChain

- OpenHands




都能接 Mem0。



---



<a id="5f32fc7c"></a>
# 四、Mem0 最大缺点



也是它最大的局限：



<a id="GhbyG"></a>
## 本质是“外挂 Memory”



即：



```
Agent Runtime
    ↓
调用 Mem0
```



所以：



**Memory 不是 Runtime Native**



这会导致：



---



**Context 不统一**



很多时候：



```
Prompt Layer
Memory Layer
RAG Layer
Tool Layer
```



是分裂的。



---



<a id="591d88a8"></a>
## 记忆只是“检索”



而不是：



**Runtime State**



这点非常关键。



Mem0 更像：



```
长期事实数据库
```



但：



**Agent 真正需要的是“运行时状态”**



例如：



```
任务做到哪一步
工具执行状态
workflow checkpoint
planner state
```



这些：



Mem0 不擅长。



---



<a id="a721d171"></a>
# 五、Viking 的思想其实更先进



字节 Viking（包括 Coze/Viking Memory 那套）：



核心思想是：



**Memory 是 Context Infrastructure**



而不是：



**一个外挂 Retrieval System**



---



Viking 更偏：



```
统一上下文引擎
```



即：



```
User Profile
Memory
RAG
Runtime State
Tool State
Task State
```



统一进入：



Context Assembly Pipeline



而不是：



```
需要时 search memory
```



---



<a id="09e4a65d"></a>
# 六、两者最本质的区别



---



<a id="B37Lp"></a>
# Mem0 是“记忆检索”



```
Memory -> Retrieval -> Prompt
```



---



<a id="DWi2F"></a>
# Viking 是“上下文编排”



```
Memory
RAG
State
Tool
Workflow
User
    ↓
Unified Context Engine
    ↓
LLM
```





---



<a id="21a89ac4"></a>
# 七、Viking 的优点



---



<a id="20f0c4f2"></a>
## 更适合 Agent



因为 Agent 不是聊天机器人。



Agent 需要：



```
状态
任务
流程
规划
checkpoint
```



而不是只有：



```
用户事实
```



---



Context Native



Memory 不再是外挂。



而是：



Prompt 构建的一部分



这个很高级。



---



<a id="7edd4400"></a>
## 更容易做复杂工作流



例如：



```
Planner
Executor
Reviewer
```



共享状态。



---



<a id="f0910dc7"></a>
# 八、Viking 的缺点



---



<a id="e0b373ba"></a>
## 耦合重



它更像：



一个完整 Runtime



不是：



```
一个 SDK
```



所以：



- 改造成本高

- 迁移难

- 灵活性差




---



<a id="3b998334"></a>
## 不够通用



Mem0：



```
接啥都行
```



Viking：



```
更偏字节自己的 Runtime 哲学
```





---



<a id="ec62cfdf"></a>
# 九、现在行业已经开始分层



---



<a id="47283791"></a>
## 第一代 Memory



```
Conversation Buffer
```



就是存聊天记录。



---



<a id="aa05648b"></a>
## 第二代 Memory



```
Vector Memory
```



embedding 检索。



---



<a id="0212a15b"></a>
## 第三代（Mem0）



```
Fact Extraction Memory
```



提取事实。



---



<a id="8a159650"></a>
## 第四代（Viking / Runtime Memory）



```
Context Operating System
```



Memory 已经不再是“数据库”。



而是：



**Agent Runtime 的一部分**



---



<a id="d7ee5a3d"></a>
# 十、业界现在的方向



<a id="X0YOj"></a>
## 更接近 Viking



而不是 Mem0。



因为大部分在做：



```
Memory
+
RAG
+
Context Engine
+
Runtime State
+
Harness
```



这已经不是：



```
memory.search()
```





而是：



**AI Runtime Architecture**



---



<a id="90e3c55f"></a>
# 十一、笔者的建议



**借鉴 Mem0 的“记忆提纯”**



**采用 Viking 的“统一上下文”**



这是目前最合理的路线。



即：



---



<a id="d8638f15"></a>
## 用 Mem0 思想解决：怎么存记忆



包括：



- fact extraction

- dedup

- entity graph

- semantic retrieval




---



<a id="bdb0f37f"></a>
## 用 Viking 思想解决：怎么组织上下文



包括：



- runtime state

- planner state

- task memory

- tool state

- context assembly




---



<a id="24fda836"></a>
# 十二、真正未来的方向



**Memory 不再是数据库问题**



而是：



**Context Engineering**



问题。



未来 Agent 拼的核心：



不是模型。



而是：



```
上下文调度能力
```



谁能：



- 用更少 token

- 注入更准上下文

- 保持长期一致性

- 管理 runtime state




谁就更强。
