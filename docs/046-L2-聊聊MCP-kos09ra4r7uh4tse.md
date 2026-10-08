# 聊聊MCP

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/kos09ra4r7uh4tse
- Slug: kos09ra4r7uh4tse
- Doc ID: 273181293
- 层级: 2
- 字数: 923
- 创建时间: 06/09/2026 07:12:36
- 更新时间: 07/09/2026 09:20:04
- 发布时间: 06/09/2026 07:12:45
- 内容更新时间: 06/09/2026 07:12:45

## 标题结构

- h1: 引子
- h2: 什么是MCP
- h2: 2. Why MCP?

## 资源链接

- a: https://zhida.zhihu.com/search?content_id=254822599&content_type=Article&match_order=1&q=Anthropic&zhida_source=entity Anthropic
- a: https://link.zhihu.com/?target=https%3A//www.anthropic.com/news/model-context-protocol Introducing the Model Context Protocol

## 正文

引子什么是MCPMCP 起源于 2024 年 11 月 25 日 Anthropic 发布的文章：Introducing the Model Context Protocol。MCP （Model Context Protocol，模型上下文协议）定义了应用程序和 AI 模型之间交换上下文信息的方式。这使得开发者能够以一致的方式将各种数据源、工具和功能连接到 AI 模型（一个中间协议层），就像 USB-C 让不同设备能够通过相同的接口连接一样。MCP 的目标是创建一个通用标准，使 AI 应用程序的开发和集成变得更加简单和统一。所谓一图胜千言，我这里引用一些制作的非常精良的图片来帮助理解：可以看出，MCP 就是以更标准的方式让 LLM Chat 使用不同工具，更简单的可视化如下图所示，这样你应该更容易理解“中间协议层”的概念了。Anthropic 旨在实现 LLM Tool Call 的标准。mcp 简单理解为保证阅读的流畅性，本文将 MCP Host / Client / Server 的定义后置。初学者/用户可暂不关注这些概念，不影响对 MCP 的使用。2. Why MCP?我认为 MCP 的出现是 prompt engineering 发展的产物。更结构化的上下文信息对模型的 performance 提升是显著的。我们在构造 prompt 时，希望能提供一些更 specific 的信息（比如本地文件，数据库，一些网络实时信息等）给模型，这样模型更容易理解真实场景中的问题。想象一下没有 MCP 之前我们会怎么做？我们可能会人工从数据库中筛选或者使用工具检索可能需要的信息，手动的粘贴到 prompt 中。随着我们要解决的问题越来越复杂，手工把信息引入到 prompt 中会变得越来越困难。为了克服手工 prompt 的局限性，许多 LLM 平台（如 OpenAI、Google）引入了 function call 功能。这一机制允许模型在需要时调用预定义的函数来获取数据或执行操作，显著提升了自动化水平。但是 function call 也有其局限性（我对于 function call vs MCP 的理解不一定成熟，欢迎大家补充），我认为重点在于 function call 平台依赖性强，不同 LLM 平台的 function call API 实现差异较大。例如，OpenAI 的函数调用方式与 Google 的不兼容，开发者在切换模型时需要重写代码，增加了适配成本。除此之外，还有安全性，交互性等问题。数据与工具本身是客观存在的，只不过我们希望将数据连接到模型的这个环节可以更智能更统一。Anthropic 基于这样的痛点设计了 MCP，充当 AI 模型的"万能转接头"，让 LLM 能轻松的获取数据或者调用工具。更具体的说 MCP 的优势在于：生态 - MCP 提供很多现成的插件，你的 AI 可以直接使用。统一性 - 不限制于特定的 AI 模型，任何支持 MCP 的模型都可以灵活切换。数据安全 - 你的敏感数据留在自己的电脑上，不必全部上传。（因为我们可以自行设计接口确定传输哪些数据）
