# 怎么评测记忆系统

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/lwvzhyiowohhg2z0
- Slug: lwvzhyiowohhg2z0
- Doc ID: 269664864
- 层级: 4
- 字数: 1172
- 创建时间: 05/13/2026 14:44:17
- 更新时间: 06/09/2026 12:07:40
- 发布时间: 05/24/2026 16:23:57
- 内容更新时间: 05/24/2026 16:23:57

## 标题结构

- h1: Memory System
- h1: 一、记忆系统应该测哪几层
- h1: 二、五层评测体系
- h2: 第一层：Store（该不该存）
- h3: Step1
- h3: Step2
- h2: 第二层：Recall（能不能记起来）
- h3: recall@k
- h3: mrr
- h2: 第三层：Consolidation（记忆的熵增：记忆会不会越来越乱）
- h3: 1:重复率
- h4: 业界标准测试方法：时间轴模拟（Time-based Simulation）
- h4: 重复率=重复记忆/此时的所有记忆
- h3: 记忆熵
- h2: 第四层：Context Assembly(上下文组装是否合理：实质是prompt调优）
- h3: Context Precision
- h2: 第五层：Graph Expansion（记忆关联是否合理）
- h3: 指标：Edge Precision

## 资源链接



## 正文

Memory System一、记忆系统应该测哪几层二、五层评测体系第一层：Store（该不该存）评测目标测试：是否正确。首先还是要构建测试集该不该记这个问题要基于你想让用户记什么？对于我们这个agi系统我们考虑下面四点1. 是否长期稳定2. 是否影响未来任务3. 是否属于用户偏好/目标/技能4. 是否具有长期学习价值​真实测试集：量化指标 Memory Precisiontp+fp：该存的+不该存的fp：垃圾记忆，无价值记忆​Precision(清晰率）=tp/(tp+fp)步骤：Step1构建测试集Step2调用记忆系统封装好的逻辑功能函数memory.store()统计存入的所有记忆数量：（tp+fp）统计其中我们标注的应该被存入的记忆数量（tp）计算 Precision(清晰率）=tp/(tp+fp)​​​第二层：Recall（能不能记起来）调用记忆系统封装好的逻辑功能函数memory.recall()这里本质复用的还是RAG的检索逻辑，所以我们测的东西就是RAG测评里的检索的指标recall@k低的话可以做如下改进： 1：query改写2：不同记忆走不同索引。例如：mrr低的话说明：1. Rerank调整重排系数2. 时间衰减改变记忆片段的加权系数​第三层：Consolidation（记忆的熵增：记忆会不会越来越乱）1:重复率业界标准测试方法：时间轴模拟（Time-based Simulation）不是单轮测。而是：​模拟用户长期聊天例如：​1000轮5000轮10000轮​然后观察：​记忆质量​持续运行 memory.store()，用脚本调用的时候记得记忆日期的填写时间设置为每天存10轮，存100天；最后看存1000轮最后的数据；首先是重复率对最后剩余的所有记忆进行向量化，然后开始聚类，相似度大于90%视为重复然后统计重复的记忆数量重复率=重复记忆/此时的所有记忆记忆熵方法对memory topic分布：做 entropy。举例好系统topic集中：entropy稳定。坏系统topic无限随机扩散：entropy不断升高。指标Shannon Entropy：​最后看两个曲线1. Memory Size Curve（记忆长度曲线）2. Retrieval Quality Curve（检索质量曲线）我们这个项目用脚本跑的真实曲线图：第四层：Context Assembly(上下文组装是否合理：实质是prompt调优）本质测：因为：很多系统：导致： token浪费  注意力污染  回答跑偏 指标：Context Precision定义怎么测用户query系统注入：真正有用的：无关context：那么：如果指标低怎么改改：第五层：Graph Expansion（记忆关联是否合理）本质测：也就是：指标：Edge Precision定义怎么测Memory系统生成边那么：如果指标低怎么改改：​
