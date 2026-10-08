# BERT与大语言模型做分类任务的区别

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/bzp9l8c8v3fgraq2
- Slug: bzp9l8c8v3fgraq2
- Doc ID: 278611018
- 层级: 3
- 字数: 971
- 创建时间: 2026-07-22T08:41:16.000Z
- 更新时间: 2026-07-25T07:54:29.000Z
- 发布时间: 2026-07-22T08:51:26.000Z
- 内容更新时间: 2026-07-22T08:51:26.000Z
- 本地同步时间: 2026-10-08T16:25:13+08:00

## 标题结构


## 资源链接


## 正文

简单说：



- **BERT 微调**：让模型从几个固定答案中选一个。

- **千问微调**：让模型逐字生成一段答案或 JSON。




**数据格式**



BERT 的训练数据非常简单：



```
{"text": "复现这篇论文并运行消融实验", "label": "Paper_Reproduction"}
{"text": "对比 LangChain 和 LlamaIndex", "label": "Framework_Evaluation"}
```



千问通常使用对话式数据：



```
{
  "messages": [
    {"role": "user", "content": "复现这篇论文并运行消融实验"},
    {
      "role": "assistant",
      "content": "{\"intent_type\":\"Paper_Reproduction\",\"entities\":{\"needs_ablation\":true}}"
    }
  ]
}
```



**训练目标**



| 对比项 | BERT 分类微调 | 千问 LoRA/SFT |
| --- | --- | --- |
| 学习目标 | 预测一个类别 | 逐字生成目标 JSON |
| 损失函数 | 分类交叉熵 | 下一个 Token 预测损失 |
| 修改参数 | 通常全量微调 | 通常只训练 LoRA 参数 |
| 输出 | 固定标签和概率 | 任意长度文本 |
| 推理方式 | 一次前向计算 | 一个 Token 一个 Token 生成 |
| JSON 稳定性 | 不涉及 JSON | 可能出现格式错误 |
| 置信度 | Softmax 概率较直接 | 很难得到可靠分类置信度 |
| 部署成本 | 较低，可用 CPU | 较高，通常更依赖 GPU |
| 能否抽实体 | 需要额外模型或规则 | 可以同时生成实体 |



**BERT 怎么训练**



```
用户文本
   ↓
Tokenizer
   ↓
BERT 编码
   ↓
取 [CLS] 向量
   ↓
线性分类头
   ↓
四个类别的概率
```



假设模型输出：



```
{
  "Paper_Reproduction": 0.96,
  "Code_Execution": 0.02,
  "Framework_Evaluation": 0.01,
  "General": 0.01
}
```



训练时只需让正确类别的概率越来越高。对于 BGE-small、mBERT 这种规模，V100 可以直接全量微调。



**千问怎么训练**



```
用户文本 + 输出格式要求
   ↓
Qwen
   ↓
逐字生成 JSON
   ↓
JSON 校验和字段检查
```



训练时不会只学习“这是论文复现”，而是学习生成完整字符串：



```
{"intent_type":"Paper_Reproduction","entities":{"needs_ablation":true}}
```



通常做法是：



1. 加载 `Qwen3-0.6B` 基础模型。

2. 在注意力层插入 LoRA 参数。

3. 冻结大部分原始参数。

4. 只训练 LoRA。

5. 保存“基础模型版本 + LoRA Adapter”。

6. 推理时加载两者并生成 JSON。

7. 后端验证 JSON，不合法时重试或回退规则。




V100 使用 `FP16`，不使用 `BF16`。0.6B 模型比较小，一般不必上复杂的 4-bit QLoRA，普通 FP16 LoRA 就够了。



**数据要求也不同**



BERT 只需要标注意图：



```
一句话 → 一个标签
```



千问需要完整标注：



```
一句话 → 意图 + 实体 + 约束 + 严格 JSON
```



例如下面这些字段都要人工确定：



```
{
  "paper_title": "Attention Is All You Need",
  "needs_ablation": true,
  "max_experiments": 3,
  "reproduction_mode": "smoke"
}
```



如果训练数据中的 JSON 风格不一致，千问就容易生成不稳定结果。因此千问的数据制作成本明显更高。



**千问也可以做普通分类**



还可以把 Qwen 当成编码器，在最后增加分类头。这样训练过程会更接近 BERT：



```
Qwen 隐藏状态 → 分类头 → 四类概率
```



但对于只有四个标签的任务，用 6 亿参数做这件事性价比不高，部署也比 BERT 重。



**这个项目的选择**



第一阶段：



```
BERT/BGE：负责主意图分类
现有规则：负责论文名、仓库地址、附件类型等确定性实体
低置信度：让用户确认或交给 LLM
```



第二阶段有足够的高质量标注数据后，再训练：



```
Qwen3-0.6B + LoRA
→ 同时输出意图、实体、约束和追问建议
```



所以两者不是简单的“旧模型和新模型”区别。BERT 更像可靠的分类零件；千问更像能够理解并填写整张表格的小 Agent。对于当前第一版意图微服务，BERT 类模型更容易做对、测准和部署。
