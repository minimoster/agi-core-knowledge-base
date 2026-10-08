# Vibe Coding 进阶之路:从氛围编程到工程化实践

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/aggayslwe4nduon6
- Slug: aggayslwe4nduon6
- Doc ID: 272934028
- 层级: 1
- 字数: 1857
- 创建时间: 06/07/2026 17:45:20
- 更新时间: 07/08/2026 02:21:57
- 发布时间: 06/29/2026 03:49:57
- 内容更新时间: 06/29/2026 03:49:57

## 标题结构

- h2: FAQ：有AI了还需要手写代码吗？
- h2: 一、重新定义 Vibe Coding:不是偷懒,是换一种方式思考
- h3: 1.核心误解澄清
- h2: 二、范式演进:从 Vibe Coding → Spec Coding → Loop Programming
- h3: 2.1 第一阶段:Vibe Coding(氛围编程)
- h3: 2.2 第二阶段:Spec Coding(规格驱动编程)
- h3: 2.3 第三阶段:Loop Programming(循环编程)
- h2: 三、Superpowers ：给 AI 加上工程纪律
- h3: Superpowers是什么？
- h4: 1.Skills (技能模块)
- h4: 2.工作流
- h2: 四、实践建议：如何从 Vibe Coding 走向工程化
- h4: 从一个主力工具开始
- h4: 为项目编写 AI 上下文文档
- h4: 构建自己的 Harness（工程约束系统）
- h2: 借鉴文章

## 资源链接

- a: https://github.com/obra/superpowers Superpowers github仓库
- a: https://mp.weixin.qq.com/s/xhQLmmn-QTHxZ7AXooZFGg Headroom：AI Agent上下文压缩层
- a: https://mp.weixin.qq.com/s/TD4QN-14GGTWdUHK5E9Dxw 现在都是Vibe Coding，那你的优势是什么
- a: https://github.com/obra/superpowers Superpowers GitHub 仓库
- a: https://blog.csdn.net/chendongqi2007/article/details/160041675 Claude Code 最佳实践

## 正文

FAQ：有AI了还需要手写代码吗？有了AI之后，不一定需要把大量时间花在手写每一行代码上，但仍然需要具备读懂代码、判断代码和修改代码的能力，开发能力的重点会从“手写代码”，转向“能清楚的表达需求"一、重新定义 Vibe Coding:不是偷懒,是换一种方式思考1.核心误解澄清误区:Vibe Coding = 让 AI 替你写代码,自己躺平 一直点击接受就行 其实不是这样的真相:Vibe Coding = 让 AI 帮你更快地实现你已经想清楚的东西关键转变:从"写代码"到"说需求、验证结果"从"技术实现细节"到"产品思维和架构设计"从"单兵作战"到"人机协作"  AI其实就相当于你的一个写代码的劳动力总之就是AI帮助你完成写代码这个动作 但是由你来为AI写出来的代码负责任 最终完成需求的开发二、范式演进:从 Vibe Coding → Spec Coding → Loop Programming2.1 第一阶段:Vibe Coding(氛围编程)工作原理:典型特征:快速迭代,感觉驱动缺乏规划,边做边改产出不稳定,质量参差不齐2.2 第二阶段:Spec Coding(规格驱动编程)核心思想:在写代码之前,先写清楚"要做什么"和"怎么做"工作流程:​核心优势:减少 AI "瞎猜"导致的返工提高代码质量和可维护性便于团队协作和知识传递2.3 第三阶段:Loop Programming(循环编程)核心突破:不再是人去 prompt AI,而是设计一个 Loop 让 AI 自动运行 Claude Code 创始人 Boris Cherny 的实践:"我不再 prompt Claude 了。我有一些 Loop 在运行,它们负责 prompt Claude、决定下一步该做什么。我的工作是写 Loop。"Agent Loop 核心机制:个人理解：Loop Programming 很像“人工约束版 ReAct”。  ReAct 的核心是：  Reason → Act → Observe → Reason → Act → Observe...  思考 → 行动 → 观察结果 → 再思考 → 再行动...  Loop Programming 在 AI 编程里的对应就是：  读代码/分析 → 修改代码 → 运行测试/命令 → 看输出 → 再分析 → 再修改...  可以把它总结成一句话：Loop Programming = 把 ReAct 用在编程任务上，并用测试/运行结果作为 Observe。总结：定义一套让 AI 反复执行的工作方式三、Superpowers ：给 AI 加上工程纪律Superpowers github仓库（目前只支持Codex、Cursor、Claude Code一些平台）Superpowers是什么？Superpowers 是一个 AI 编程脚手架框架，让 AI 在写代码之前先像资深工程师一样思考、规划和验证。GitHub Stars:225k+(2026年数据)核心价值:把 AI 从"听话但毛躁的实习生"升级为"按流程办事的资深工程师"核心组成：1.Skills (技能模块)20+ 个预定义的工程化技能:举例：你说 "帮我做一个计划清单的web项目"Agent 应先触发 brainstorming — 提问、给方案、写设计文档，不写代码你确认设计后 → writing-plans — 拆成 2–5 分钟的小任务你说 "开始" → subagent-driven-development — 派子 Agent 逐项实现实现时 → test-driven-development — 先写失败测试，再写代码 （测试驱动开发）完成后 → verification-before-completion — 跑测试验证，不能空口说"修好了"2.工作流强制的多阶段流程:完整工作流:用户反馈:"前面花了两个小时被拷打需求,后面执行只用了 10 分钟,一遍过。"四、实践建议：如何从 Vibe Coding 走向工程化从一个主力工具开始先选 Cursor、Claude Code 或 Trae 其中之一深入使用，比浅尝十个工具更重要。为项目编写 AI 上下文文档至少写清楚：项目背景、技术栈、目录结构、常用命令、测试方式、代码规范、禁止事项。构建自己的 Harness（工程约束系统）真正的进阶是构建围绕 AI 的工程 Harness，包括：上下文加载、任务拆解、工具调用、测试执行、结果验证、人工审批、知识沉淀。有了 Harness，AI 才不只是聊天窗口，而是可以被纳入工程流程的生产力系统。借鉴文章Headroom：AI Agent上下文压缩层现在都是Vibe Coding，那你的优势是什么 Superpowers GitHub 仓库 Claude Code 最佳实践 ​
