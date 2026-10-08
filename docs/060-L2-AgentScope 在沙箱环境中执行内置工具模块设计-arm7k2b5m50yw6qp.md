# AgentScope 在沙箱环境中执行内置工具模块设计

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/arm7k2b5m50yw6qp
- Slug: arm7k2b5m50yw6qp
- Doc ID: 276449765
- 层级: 2
- 字数: 1980
- 创建时间: 07/05/2026 08:31:39
- 更新时间: 07/05/2026 08:33:38
- 发布时间: 07/05/2026 08:32:04
- 内容更新时间: 07/05/2026 08:31:53

## 标题结构



## 资源链接



## 正文

https://github.com/agentscope-ai/agentscope/issues/1826https://github.com/agentscope-ai/agentscope/pull/1943🥷我当时做的是 AgentScope 里 Docker/E2B workspace 的 built-in tools 能力补齐。这个问题表面上是给沙箱环境加几个工具，但往下看，其实是一个架构边界问题：Agent 看到的 Bash、Read、Write、Edit、Grep、Glob 这些工具，怎么在 Docker/E2B 里执行，同时又不破坏 LocalWorkspace（本地） 已经有的权限、状态和调用语义。S，背景AgentScope 原来有几类 workspace。LocalWorkspace 可以直接暴露六个 built-in tools，Agent 能用 Bash 跑命令，用 Read/Write/Edit 操作文件，用 Grep/Glob 搜索代码。但 Docker/E2B workspace 是隔离环境。文件系统、进程、依赖都在容器或云沙箱里。这里就有一个问题：如果直接复用本地的 built-in tools，它们会在 host 上执行，读写的是本地文件，不是沙箱里的 /workspace。如果干脆不暴露这些工具，Docker/E2B 又和 LocalWorkspace 能力不一致，Agent 进入沙箱后反而缺少最基础的文件和命令能力。当时 issue 里的提法更接近“给 Docker/E2B 加 dedicated built-in MCP server”。所以我一开始也沿着这个方向分析：应该在沙箱里跑一个内置 MCP server，把 Bash/Read/Write/Edit/Grep/Glob 包成 MCP tools，再让host侧的 Agent 通过已有的 MCP gateway 转发调用。T，目标目标不是简单让工具能跑，而是让这六个 built-in tools 在沙箱里跑，并且尽量保持 LocalWorkspace 的语义一致。这里有几个约束比较关键。Read/Edit/Write 不是普通文件 API，它们依赖 AgentState.tool_context.read_file_cache。Read 读完文件后会缓存内容，Edit 要求先读过文件再编辑，Write 覆盖已有文件前也要求先读过。这个语义是 AgentScope built-in tools 的一部分。权限也一样。Bash、Edit、Write 都有自己的 check_permissions()，Toolkit 在执行 tool 前会走 permission engine。不能因为换成沙箱执行，就把这些语义绕掉。所以当时给定的判断标准是：最终方案要满足三个条件。第一，工具实际执行位置要在 Docker/E2B 内部；第二，Agent 看到的工具仍然是原来的六个 built-in tools；第三，权限和 state injection 不能丢。A，行动和设计决策我先把现有链路梳理了一遍。get_toolkit() 会从 workspace.list_tools() 拿 workspace 自己提供的 tools，也会从 workspace.list_mcps() 拿 MCP clients。Toolkit 里再把普通 tools 和 MCP tools 合并成可用工具集合。这一步让我确认了一件事：外部 MCP gateway 本来就是通的，问题不在 list_mcps()，而在 Docker/E2B 的 list_tools() 没有提供 LocalWorkspace 那套 built-in tools。接着我对比了两个方案。第一个方案是我们一开始讨论的 built-in MCP server。沙箱里跑一个 server，host 侧再做 proxy。这个方案直观，也贴近 issue 的表述。但我继续往下推时发现它有一个隐患：如果把 built-in tools 当普通 MCP tools 处理，Toolkit.call_tool() 不会给它们注入 _agent_state，Read/Edit/Write 的 read-before-edit/write 语义会丢。为了补回来，host 侧还得再做一层 proxy，真正执行时，再把请求转给沙箱里的 MCP server。这样就变成了 sandbox-side MCP server 加 host-side proxy，两边都要做适配。架构就变重了（可以看到图中有4层，三次转发）。这时我重新拆了一下问题：这六个 tools 真正依赖沙箱的是什么？拆完以后发现，真正变化的只有三件事：命令在哪里执行，文件从哪里读，文件写到哪里。也就是：其他能力，比如 file_exists、is_dir、delete_path、stat_mtime，都可以建立在这三个能力上。所以最终更合理的设计是抽一层 BackendBase。built-in tools 仍然是原来的 ToolBase，继续负责 schema、权限、状态、结果格式；backend 只负责把底层执行切到 Local、Docker 或 E2B。最后落地成三类 backend：然后 workspace 负责接线。LocalWorkspace 创建 LocalBackend，DockerWorkspace 在 container 启动后创建 DockerBackend，E2BWorkspace 在 sandbox attach/create 后创建 E2BBackend。它们的 list_tools() 都返回同样六个 built-in tools，只是传进去的 backend 不同。这里还有一个我觉得很关键的小设计：Docker/E2B 的 list_tools() 如果发现 backend 还没初始化，会直接抛错，而不是让工具默认 fallback 到 LocalBackend。否则最危险的情况是工具表面能跑，但实际在 host 上跑，这比直接报错更难排查。Glob 也做了一个单独处理。它没有要求远端环境 import 完整 AgentScope 包，而是拆了一个独立 _glob_helper.py 脚本。Docker/E2B 初始化时把这个脚本放进沙箱，Glob 通过 backend 执行它。这样远端依赖面很小，也避免因为 Python 包、import 图、版本不一致导致工具不可用。R，结果最终 PR #1903 合入后，Docker/E2B workspace 可以像 LocalWorkspace 一样暴露 Bash、Read、Write、Edit、Grep、Glob 六个 built-in tools。Agent 看到的工具名、schema、权限判断、read-before-edit/write 语义都保留下来了，但实际命令和文件操作发生在容器或 E2B sandbox 内部。这次设计也给后续扩展留下了一个比较清楚的模式。比如后来如果要支持 Daytona workspace，就不需要重新设计六个 tools，也不需要再做一套 built-in MCP server。只要实现一个 DaytonaBackend，把 Daytona SDK 的命令执行、文件读取、文件写入适配到 BackendBase，再让 workspace 的 list_tools() 返回绑定这个 backend 的六个 built-in tools 就可以。我觉得这个项目里最重要的设计决策，是没有被 issue 字面上的 MCP server 带着走。MCP gateway 仍然保留给外部 MCP tools，AgentScope 自己的 built-in tools 则保持 workspace-native。最终抽出来的不是协议层，而是执行后端层。这样改动更小，语义更稳，也更适合后续接新的沙箱 provider。如果面试官追问我“为什么不用 MCP 方案”，我会这样补充：MCP 方案不是不能做，它的问题是抽象位置偏高。我们要解决的是执行环境切换，不是工具协议接入。Read/Edit/Write 这种 built-in tools 有 AgentScope 自己的状态语义，普通 MCPTool 拿不到 _agent_state。如果强行走 MCP，就必须再做 host proxy 把语义补回来。相比之下，BackendBase 直接把变化点压缩到 exec_shell/read_file/write_file 三个能力上，tool 语义完全留在原地，这个边界更干净。如果面试官追问我“这个方案有什么风险”，我会说：一个风险是路径命名空间。host 上的路径和 sandbox 里的 /workspace 不是一套东西，权限系统里 working directory 的判断要明确使用 agent 可见的 workspace path。另一个风险是远端依赖，比如 Grep 依赖 ripgrep，Glob 依赖 helper 脚本，所以 Dockerfile 和 E2B bootstrap 必须把这些依赖准备好。PR 里也对应加了 backend 层测试、Docker/E2B live 测试，以及 workspace list_tools() 接线测试，防止工具静默跑到错误环境里。
