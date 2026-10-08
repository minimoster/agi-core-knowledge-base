# 算秩未来 云原生agent1面

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/aonro39365zmn097
- Slug: aonro39365zmn097
- Doc ID: 282618808
- 层级: 1
- 字数: 686
- 创建时间: 2026-08-25T07:48:58.000Z
- 更新时间: 2026-09-28T08:23:10.000Z
- 发布时间: 2026-09-28T08:23:09.000Z
- 内容更新时间: 2026-09-28T08:23:09.000Z
- 本地同步时间: 2026-10-08T16:25:13+08:00

## 标题结构

- h1: docker
- h2: Go 语言板块
- h2: 算法&链表
- h2: Java 板块
- h2: 网络 HTTP
- h2: 网络‑DNS
- h2: 架构&业务设计题（财务系统安全整改）

## 资源链接


## 正文

这是一个什么勾八公司，问的几把好难，还给老子一面挂了我日。



<a id="docker"></a>
# docker



1. Docker 的隔离是谁实现的？仅仅更换 runtime（runc → runsc/gVisor）意味着什么？

2. 容器进程默认是以 root 身份运行的吗？

3. docker 命令是客户端程序吗？它和谁通信？

4. gVisor里面的 Sentry 和 sentry.io 是什么关系，是不是同一个东西？

5. 你了解deepseek的沙箱和codex的沙箱原理和设计吗

6. docker的一整个架构




<a id="4d2a283e"></a>
## Go 语言板块



1. channel：带缓冲区 / 无缓冲 channel；如何**非阻塞尝试写入 channel，不阻塞协程**；为什么不能靠 `len(ch)<cap(ch)` 判断能不能写

2. close(channel)：多次关闭同一个 channel 会发生什么（panic）

3. panic & recover：子 goroutine 发生 panic，父协程的 recover 能不能捕获？为什么？正确的子协程兜底写法

4. recover 的边界：就算 defer+recover 写在同一个协程，依然无法捕获的错误类型（fatal error，典型例子：map并发读写），普通panic与runtime致命错误区别

5. `any`（空接口 `interface{}`）是什么、使用坑点、接口nil陷阱、泛型里 `T any` 的含义

6. Go 是否支持函数重载 overload；重载概念；Go替代重载的方案；重载 vs 重写(override)区分




<a id="496c1a76"></a>
## 算法&链表



1. **两个****单向链表****相交（LeetCode160）**：相交的定义（内存地址相等，不是值相等）；第一个交点是否唯一；双指针最优解法思路 + 代码

2. 两个**有序****单向链表**找公共元素（按数值匹配，节点独立）；双指针归并思路；值可以重复




<a id="b8269983"></a>
## Java 板块



1. BigDecimal：三种构造方式对比：传入 `double` / `long` / `String`；精度丢失问题；资金业务最佳实践；`compareTo`与`equals`坑点




<a id="cf67a9ae"></a>
## 网络 HTTP



1. HTTP动词 PUT / POST 的语义区别；PUT全量替换、幂等性；PATCH局部更新；幂等概念




<a id="53fc7b4a"></a>
## 网络‑DNS



1. DNS协议作用；默认传输协议、端口(UDP 53)；什么时候走TCP‑53；递归查询、迭代查询；域名解析整条链路




<a id="0fe54086"></a>
## 架构&业务设计题（财务系统安全整改）



场景：老用户密码无盐MD5哈希，审计要求1年内升级到bcrypt，**不能打扰用户**，给出完整落地方案



- 核心方案：惰性懒迁移

- 表改造、双版本兼容、登录时无感知升级、沉睡过期账号处理、并发风险、审计合规策略
