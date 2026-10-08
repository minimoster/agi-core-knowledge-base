# CoT与ReAct

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/syui4pdqhx72ugen
- Slug: syui4pdqhx72ugen
- Doc ID: 276750562
- 层级: 1
- 字数: 945
- 创建时间: 2026-07-07T09:14:02.000Z
- 更新时间: 2026-07-07T09:17:55.000Z
- 发布时间: 2026-07-07T09:17:28.000Z
- 内容更新时间: 2026-07-07T09:17:28.000Z
- 本地同步时间: 2026-10-08T16:25:13+08:00

## 标题结构

- h2: Planning 基础
- h2: Chain-of-Thought
- h2: ReAct
- h2: LLM+P（需要了解什么是LLM + P）
- h2: Tree of Thoughts
- h2: 方法比较与应用
- h2: 排错与综合设计

## 资源链接


## 正文

<a id="b8539713"></a>
## Planning 基础



1. Agent 中的 Planning 是什么？

2. Planning 和 Reasoning 有什么区别？

3. 为什么 Agent 需要 Planning？

4. Planning 通常需要解决哪些子问题？

5. 什么情况下固定 workflow 比动态 planning 更合适？

6. Planning 方法为什么需要停止条件？

7. Planning 失败会导致哪些问题？

8. 如何评估一个 plan 的质量？

9. Planning 与 tool use 有什么关系？

10. Planning 与 memory 有什么关系？






<a id="chain-of-thought"></a>
## Chain-of-Thought



11. 什么是 Chain-of-Thought？

12. CoT 为什么能提升复杂推理任务表现？

13. CoT 适合哪些任务？

14. CoT 不适合哪些任务？

15. few-shot CoT 和 zero-shot CoT 有什么区别？

16. CoT 和普通逐步说明有什么区别？

17. CoT 的主要风险有哪些？

18. 为什么 CoT 可能出现“推理链看似合理但答案错误”？

19. 生产系统中为什么不一定展示完整 CoT？

20. CoT 如何和 self-consistency 结合？






<a id="react"></a>
## ReAct



21. ReAct 的核心思想是什么？

22. ReAct 中 Reasoning 和 Acting 分别指什么？

23. ReAct 的典型模式是什么？

24. ReAct 相比纯 CoT 的优势是什么？

25. ReAct 适合哪些任务？

26. ReAct 不适合哪些任务？

27. ReAct 中 Observation 的作用是什么？

28. 为什么 ReAct 可以降低模型仅凭先验回答的风险？

29. ReAct 的常见失败模式有哪些？

30. 生产系统中如何实现安全的 ReAct？






<a id="b29e8b4e"></a>
## LLM+P（需要了解什么是LLM + P）



31. LLM+P 的核心思想是什么？

32. LLM+P 中 LLM 和 classical planner 分别负责什么？

33. 什么是 PDDL？它在 LLM+P 中有什么作用？

34. LLM+P 适合哪些任务？

35. LLM+P 不适合哪些任务？

36. LLM 把自然语言转成规划问题时可能出现哪些错误？

37. 经典 planner 的优势是什么？

38. LLM+P 的工程难点有哪些？

39. LLM+P 和 ReAct 有什么区别？

40. 什么时候应该优先考虑符号规划而不是纯 LLM planning？






<a id="tree-of-thoughts"></a>
## Tree of Thoughts



41. Tree of Thoughts 的核心思想是什么？

42. ToT 中的 thought 是什么？

43. ToT 和 CoT 的主要区别是什么？

44. ToT 适合哪些任务？

45. ToT 的主要成本是什么？

46. ToT 可以结合哪些搜索策略？

47. BFS、DFS、Beam Search 在 ToT 中分别有什么特点？

48. ToT 中评估函数可以来自哪里？

49. ToT 为什么适合需要回溯的问题？

50. ToT 在生产系统中有什么限制？






<a id="79da3be4"></a>
## 方法比较与应用



51. 请比较 CoT、ReAct、LLM+P、ToT 的输入输出形式。

52. 请比较 CoT、ReAct、LLM+P、ToT 的成本。

53. 请比较 CoT、ReAct、LLM+P、ToT 的可控性。

54. 哪些方法适合需要外部信息的任务？

55. 哪些方法适合状态和动作可形式化的任务？

56. 哪些方法适合多候选路径探索？

57. 如何为数学题选择 planning 方法？

58. 如何为网页问答选择 planning 方法？

59. 如何为机器人任务选择 planning 方法？

60. 如何为代码调试任务选择 planning 方法？






<a id="55af591f"></a>
## 排错与综合设计



61. 如果 CoT 输出冗长且错误，你会如何改进？

62. 如果 ReAct 频繁选错工具，你会如何改进？

63. 如果 ReAct 忽略 Observation，你会如何处理？

64. 如果 LLM+P 生成的 PDDL 错误，你会如何排查？

65. 如果 ToT 成本太高，你会如何优化？

66. 如何为 Planning 系统设计 trace？

67. 如何给 Planning 加 verifier？

68. 请设计一个结合 ReAct 和 rerank 的文档问答 Agent。

69. 请设计一个结合 ToT 和工具验证的复杂推理系统。

70. 请系统总结 CoT、ReAct、LLM+P、ToT 的原理、适用场景、优缺点和面试表达要点。
