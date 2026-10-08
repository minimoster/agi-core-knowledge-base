# Spark 快速入门

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/fgrxr4yg4rfn1q65
- Slug: fgrxr4yg4rfn1q65
- Doc ID: 275777282
- 层级: 2
- 字数: 6487
- 创建时间: 06/30/2026 02:50:45
- 更新时间: 07/05/2026 07:19:27
- 发布时间: 06/30/2026 02:57:14
- 内容更新时间: 06/30/2026 02:57:14

## 标题结构

- h2: Spark 的核心抽象：三句话
- h1: 一个例子快速学习Spark
- h2: Background
- h2: 物理层面发生了什么
- h2: Shuffle Join 的完整链路
- h2: Broadcast Join
- h2: join顺序
- h1: 一段代码快速学习Spark
- h1: 特征穿越（Feature Leakage）
- h2: Background
- h2: 解决方案
- h1: 数据倾斜（Data Skew）
- h2: Background
- h2: 加盐打散（Salting）

## 资源链接



## 正文

Spark 的核心抽象：三句话Spark 的编程模型其实非常简练：第一，数据被抽象成一张分布式的表（DataFrame），逻辑上是一张表，物理上被切成很多 partition 散布在不同机器上。 这跟你熟悉的 pandas DataFrame 概念一样，只是底层分布式了。第二，你对这张表的所有操作（filter、join、groupBy、select……）都不会立即执行，而是被记录成一个 DAG（有向无环图）。 这就是 lazy evaluation——Spark 攒着你的操作，直到你要一个具体结果（比如 write、count、show）的时候，才把整个 DAG 提交给调度器优化执行。第三，DAG 会被切分成若干 Stage，Stage 的边界就是 Shuffle。 同一个 Stage 内的操作可以在每台机器上独立执行（称为 narrow dependency），不需要网络通信；而跨 Stage 就意味着数据要在网络上重新分配。一个例子快速学习SparkBackground假设你有一份用户行为日志，每天几十亿条，存在 HDFS（分布式文件系统）上，格式大概是：现在你要算一个特征：每个用户过去 7 天的点击次数。用 Spark（PySpark）写出来就是：这段代码看着跟 pandas 几乎一样。但底层发生的事情完全不同，我带你一步步看。物理层面发生了什么起点：数据是怎么存的？那几十亿条日志不在一台机器上。它们被切成了很多文件块（比如 128MB 一个），分散在几十甚至几百台机器的磁盘上。每一个文件块对应 Spark 里的一个 partition。假设你有 1000 个 partition，分布在 100 台机器上。第一步：filter（本地操作，无网络通信）.filter(col("action") == "click") 和 .filter(col("timestamp") >= ...) 这两步，每台机器只需要扫描自己本地的 partition，把不满足条件的行丢掉。没有任何数据需要在机器之间传输。这就是 narrow transformation——每个 partition 独立处理，不依赖其他 partition 的数据。第二步：groupBy + count（Shuffle 来了）到 .groupBy("user_id").agg(count(...)) 这一步，问题出现了：同一个 user_id 的行为记录可能散布在很多台机器上（因为日志是按时间切块存储的，不是按 user_id 组织的）。要按 user_id 聚合，Spark 必须把同一个 user_id 的数据搬到同一台机器上。这就是 Shuffle。具体来说：Spark 对每条记录的 user_id 算一个 hash，决定它应该去哪个目标 partition。然后每台机器把自己持有的数据按目标 partition 分类，通过网络发送给对应的机器。接收方收到之后再做本地聚合。这个过程的代价是巨大的——网络带宽、磁盘 IO（Shuffle 的中间数据会落盘）、序列化开销。在真实生产环境中，一个大表的 Shuffle 可能需要传输 TB 级别的数据。第三步：join你算出了每个用户的 click_count_7d，现在你要把这个特征拼回到训练样本上。训练样本表长这样：你需要按 user_id 把 click_count_7d join 上去：这个 join 底层又是一次 Shuffle——两张表都要按 user_id 重新分区，把 user_id 相同的行发到同一台机器上才能匹配。起点：两张表，各自分散在集群里左表 samples（训练样本，非常大，比如 100 亿条）：这张表被切成了比如 2000 个 partition，分布在集群的几百台机器上。关键点：这些 partition 是按什么切的？ 通常是按文件块大小切的（比如每 128MB 一个 partition），跟 user_id 没有任何关系。所以同一个 user_id 的样本散布在很多 partition 里。右表 click_count（你刚算出来的用户特征，较小，几亿行）：这张表也被分成了若干 partition，同样跟 user_id 的分布没有确定的对应关系。现在你要执行 samples.join(click_count, on="user_id", how="left")。join 的语义是： 对于 samples 里的每一行，根据它的 user_id，去 click_count 里找到 user_id 相同的那一行，把 click_count_7d 拼过来。找不到就填 null（因为是 left join）。Shuffle Join 的完整链路这是 Spark 默认的 join 策略，叫 Sort-Merge Join。分三个阶段：阶段一：两张表各自按 join key 做 Shuffle（Map 阶段的输出）Spark 决定一个目标分区数（比如 200 个分区）。对于两张表里的每一行，Spark 都用同一个 hash 函数计算 hash(user_id) % 200，得到一个目标分区号。然后每台机器把自己持有的数据按目标分区号分组，写到本地磁盘上（这些叫 Shuffle Write 文件）。这一步结束后，逻辑上你可以这样理解：核心保证：同一个 user_id，因为用了同一个 hash 函数，一定会被分到同一个目标分区。 这意味着分区 0 里的 samples 行只需要跟分区 0 里的 click_count 行匹配，不需要关心其他分区的数据。阶段二：Shuffle Read（数据通过网络搬运）每个目标分区由一台机器负责处理。这台机器需要通过网络，从集群中所有机器那里拉取属于自己那个分区的数据。比如负责目标分区 0 的机器，需要从几百台机器上各自拉取"那些 hash(user_id)%200==0 的行"——来自 samples 的和来自 click_count 的都要拉过来。这就是 Shuffle 最贵的地方： 全集群范围的网络数据传输。两张表的数据都要动。阶段三：本地 Sort-Merge（真正做匹配）目标分区 0 的负责机器现在拥有了两边表中所有 hash 到分区 0 的数据。接下来：把来自 samples 的数据按 user_id 排序把来自 click_count 的数据按 user_id 排序两个有序序列做归并——两个指针同时往前走，user_id 相等就拼在一起输出这就是"Sort-Merge"的含义。排序之后归并匹配的时间复杂度是 O(n log n)，非常高效。整体画面两张表的数据都通过网络做了全量搬运。 这就是代价所在——samples 有几百 GB 甚至 TB，click_count 虽然小但也参与了 Shuffle，整个过程涉及的网络传输量约等于两张表大小之和。但这里有一个关键的情况差异：click_count 这张表其实很小（每个 user_id 只有一行，几亿用户撑死也就几个 GB），而 samples 表可能非常大（几十亿条样本，几百 GB 甚至 TB 级别）。那有什么办法可以让大表数据不动，只让click_count表的数据移动呢？PS：这里还要注意user的信息也是分布式存储在不同机器的多个partition上的。Broadcast Join换个角度想：click_count 只有几个 GB。几个 GB 在现代服务器上意味着什么？每台机器的内存通常是 64GB、128GB 甚至更多。几个 GB 的数据，每台机器都装得下。所以方案就变成了：把 click_count 这张小表完整地复制一份到每台机器的内存里。 然后大表的每个 partition 就地跟本地内存中的小表做 join，根本不需要任何 Shuffle。这就是 Broadcast Join（广播 join）：大表完全没动。 没有 Shuffle，没有网络上搬运几百 GB 的数据。唯一的网络开销是把那几个 GB 的小表广播出去，而且这个广播用的是类似 BitTorrent 的 P2P 协议，不是一台机器发给所有机器，而是像树状传播一样扩散，非常快。代价从"两张表都做全量 Shuffle（几百 GB 网络传输）"变成了"只广播小表（几个 GB 网络传输）"。在搜广推场景下，这个优化是日常操作——因为你的样本表永远是最大的，而很多特征表（用户画像、item 属性）相对小得多。join顺序Broadcast Join 不影响分区所以不需要在意它的位置，但 Shuffle Join 之间的顺序是要在意的——应该把同一个 key 的 Shuffle Join 放在一起。假设你有这样一个 pipeline：这里 Broadcast Join 插在两个 Shuffle Join 中间，没有破坏分区。所以第三步可以跳过 Shuffle。这就是你说的"不冲突"。但如果换个情况：这里第三步就必须重新 Shuffle 了，因为第二步按 item_id 把分区打乱了。一段代码快速学习Spark​特征穿越（Feature Leakage）Background假设你在 1 月 15 日跑 pipeline，生成过去 7 天（1月8日-1月15日）的训练样本。其中有一条样本是：你要给这条样本拼上一个特征："用户 A 过去 7 天的点击次数"。问题来了：这个"过去 7 天"是相对于什么时间点？如果你直接用 filter(timestamp >= 1月8日) 然后 groupBy 算出用户 A 的 click_count_7d，这个值包含了 1 月 10 日到 1 月 15 日之间用户 A 的行为。但这条样本发生在 1 月 10 日——你在训练数据里用了这条样本发生之后的信息。 模型在线上推理的时候，1 月 10 日那个瞬间不可能知道用户未来 5 天会做什么。这就是特征穿越：训练时用了预测时不可能获得的信息。后果是模型的离线指标虚高，上线后效果大幅下降。正确的做法是：每条样本的特征，必须只用该样本 event_timestamp 之前的数据来计算。 也就是说，1 月 10 日的样本，它的 click_count_7d 应该是 1 月 3 日到 1 月 10 日之间的点击次数。解决方案精确性 vs 计算代价。为每条样本单独算一份特征，精确但极其昂贵——100 万条样本，每条都要对日志做一次 filter + groupBy，这在 Spark 里是不现实的。但用全局特征一把 join 上去，快但有穿越。那工业界实际是怎么做的？通常有两种策略，它们在精确性和代价之间取了不同的折中点：策略一：特征快照（Feature Snapshot）。 每天凌晨用 Spark 跑一次特征计算，把"截至昨天 24:00"的用户特征存成一张快照表，打上日期标签。然后样本按日期 join 对应那天的快照。比如 1 月 10 日的样本 join 1 月 9 日的快照。这样不是完全精确（同一天内的样本共享同一份特征），但穿越被控制在了天级别。策略二：窗口函数（Window Function）。 把样本表和日志表按 user_id join 到一起，然后用 Spark 的 Window 函数，对每条样本只聚合"该样本时间戳之前"的行为记录。这在逻辑上完全精确，但计算代价比策略一大得多。工业界大多数推荐系统的离线训练用的是天级快照，也就是策略一。原因很现实：天级快照的计算代价远小于窗口函数，而推荐模型对小时级的特征穿越其实不太敏感——用户的短期兴趣画像在几个小时内变化不大，天级的误差对 AUC 的影响通常在小数点第四位。数据倾斜（Data Skew）Background前 1% 的用户贡献了 30% 的行为量，这是现实世界的分布。在真实的推荐系统里，这个比例更极端：抖音的头部创作者一天可能产生几万条互动记录，而大量沉默用户可能一周只有几条曝光。在 Spark 当中，这意味着什么呢？当你执行 groupBy("user_id").agg(...) 的时候，Shuffle 按 hash(user_id) % N 分区。大部分分区可能只有几万条数据，几秒就算完了。但有些分区碰巧装了几个超级活跃用户，数据量可能是其他分区的 100 倍。Spark 的 Stage 必须等所有 Task 都完成才能进入下一步——于是整个任务被那几个慢 Task 卡住，可能 99 个 Task 在 1 分钟内完成，最后那 1 个 Task 跑了 30 分钟。这就是数据倾斜。它不是代码逻辑错误，一切结果都是正确的，但任务就是跑不动。问题真正痛的场景是 join 时的数据倾斜。比如你要把行为日志按 user_id join 到样本表上。某个超级活跃用户在样本表里有 1000 条样本，在行为日志里有 50000 条记录。Shuffle Join 会把这两边数据都搬到同一个分区，这个分区要处理 1000 × 50000 的匹配——而普通用户可能只有 10 × 50 的匹配。加盐打散（Salting）核心思路就是你说的"拆散大 key"。具体操作分两步：对大表（行为日志）加随机后缀：对小表（样本表）做膨胀复制：然后按 salted key 做 join：现在 user_id=A 的 50000 条日志被分散到了 10 个分区，每个分区只处理约 5000 条。倾斜被打散了。总结样本表膨胀了 10 倍。这就是 salting 的 trade-off：用空间（膨胀小表）换时间（打散大 key）。而且你需要事先知道哪些 key 是倾斜的，盐的粒度（10 还是 100）也需要调。这引出一个实际问题：在真实生产环境中，你往往不会对所有 key 都加盐——因为 99% 的 key 根本没有倾斜，给它们加盐只是浪费。 更常见的做法是：先统计哪些 key 是热点（比如行为数 > 10000 的 user_id），只对这些热点 key 做 salting，其余 key 走正常 join，最后 union 两部分结果。
