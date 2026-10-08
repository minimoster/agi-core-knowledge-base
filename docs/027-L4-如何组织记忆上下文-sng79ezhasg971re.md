# 如何组织记忆上下文

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/sng79ezhasg971re
- Slug: sng79ezhasg971re
- Doc ID: 273927898
- 层级: 4
- 字数: 1415
- 创建时间: 06/15/2026 03:41:06
- 更新时间: 06/25/2026 00:52:30
- 发布时间: 06/15/2026 07:06:25
- 内容更新时间: 06/15/2026 07:06:25

## 标题结构

- h2: 拼接流程
- h2: 核心概念
- h3: SlotKind六种槽位
- h3: SlotFilter含义
- h3: RuntimeContextSchema：Mode 与槽位编排表
- h3: ContextSource：槽位数据提供者
- h2: 装配核心流程
- h2: 渲染
- h2: 与"普通字符串拼接"的差异
- h2: 主流程的使用
- h3: 装配阶段（agent 启动时一次）
- h3: 开始前注入
- h2: 总结

## 资源链接



## 正文

我们的项目是通过promptctx包构建System Prompt，职责是：每轮 LLM 推理之前，按当前 Mode 编排出"喂给模型的 System Prompt 前缀"。拒绝字符串之间拼接。拼接流程核心概念SlotKind六种槽位其中六种槽位代表含义如下：SlotFilter含义RuntimeContextSchema：Mode 与槽位编排表ModeConstraintsProfilePlannerTaskMemToolStateRecallchat✓✓———✓tool✓✓——必填✓rag✓✓———✓react必填✓必填✓必填✓未知 Mode 自动 fallback 到 chat 做兜底。例子： ContextSource：槽位数据提供者一个 source 可支持多个 SlotKind（如 GraphMemory 同时填 Profile/Recall）各 source 独立可测，按 SlotKind 注册到SourceRegistry装配核心流程双层 Budget 控制单槽位 budget：source 自治，超额自动截断全局 budget：默认2400字符，超限按优先级裁剪：保证安全约束的优先级最高，永远在提示词中不丢失。 渲染rc.Render() 把 FilledSlot 渲染为 zh-CN System Prompt 前缀：Skipped 或 Items 为空的槽位不渲染。与"普通字符串拼接"的差异维度普通做法promptctx组织方式按数据类型（history/docs/tools）按认知槽位（profile/planner/...）Mode 区分一套 prompt 走天下Schema 驱动，4 Mode 各取所需召回策略Top-K 全塞SlotFilter 声明式过滤预算控制估个总长度截断双层 budget + 优先级裁剪安全约束可能被截断丢失Constraints 优先级 0，永不丢数据获取串行goroutine 并发可测试性拼好的字符串难断言每个 source 独立单测主流程的使用装配阶段（agent 启动时一次）开始前注入 memPrefix 在每轮 ReAct 开始前装配一次、冻结复用；promptctx 内部缓冲区在循环中实时更新（任务步骤与工具调用结果），但这些更新会"延迟一轮"才出现在 prompt 里——属于跨轮次的短期工作记忆。总结避免上下文污染（最大的收益）恢复 Agent 状态，而不是恢复聊天记录Token 利用率更高长任务能力更强让不同记忆有不同生命周期​
