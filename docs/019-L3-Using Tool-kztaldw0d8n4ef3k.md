# Using Tool

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/kztaldw0d8n4ef3k
- Slug: kztaldw0d8n4ef3k
- Doc ID: 269189858
- 层级: 3
- 字数: 136
- 创建时间: 05/10/2026 19:00:48
- 更新时间: 06/16/2026 11:21:29
- 发布时间: 05/12/2026 13:04:19
- 内容更新时间: 05/12/2026 13:04:19

## 标题结构

- h3: Tool Agent（工具调用）

## 资源链接



## 正文

Tool Agent（工具调用）能讲的点：Function Calling 原理：给 LLM 提供工具 Schema（name/description/parameters），LLM 决定是否调用及参数填写工具三要素：名称、描述（越清晰 LLM 越准确）、参数 Schema（JSON Schema 格式）面试常问："如何保证工具调用的安全性？" → 参数校验、白名单工具、权限控制、执行沙箱"工具调用失败怎么处理？" → 引出 Stage 6 Harness 的重试机制
