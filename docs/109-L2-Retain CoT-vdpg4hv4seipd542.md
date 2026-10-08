# Retain CoT

- 原始链接: https://www.yuque.com/yuqueyonghu-ng3vtk/agi-saber/vdpg4hv4seipd542
- Slug: vdpg4hv4seipd542
- Doc ID: 282375235
- 层级: 2
- 字数: 478
- 创建时间: 2026-08-23T07:21:41.000Z
- 更新时间: 2026-08-23T07:29:17.000Z
- 发布时间: 2026-08-23T07:22:09.000Z
- 内容更新时间: 2026-08-23T07:22:09.000Z
- 本地同步时间: 2026-10-08T16:25:13+08:00

## 标题结构

- h1: GPT
- h2: turn和step的区别
- h1: Qwen
- h1: 总结

## 资源链接

- [语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716056-34bfb6fd-6629-4bcc-8f8c-ebdbc363fd89.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_34%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)
- [语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716236-86932199-92d8-48c5-b028-6637f4e34d9b.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_54%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)
- [语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469717418-678d6cbc-022f-4ff6-b3b0-bfdc6c86b24f.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_17%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)
- [语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716920-781e456d-a104-46c7-8120-84fb28217523.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_71%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)
- [语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716970-bd92f148-b2d2-4874-a714-6127bdf8cf63.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_56%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)
- [语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469718409-d1ceca6e-d225-438e-ad33-5aa59deaccd8.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_52%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)

## 正文

<a id="gpt"></a>
# GPT



![语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716056-34bfb6fd-6629-4bcc-8f8c-ebdbc363fd89.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_34%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



我们注意到，每次游戏操作后，所有私有推理都会被丢弃。这意味着 GPT‑5.6 Sol 每次行动时都必须重新理解游戏，无法记住之前的思考。模型仍能看到过去的操作记录和简短附注，但看不到促成这些操作的计划、见解或思考。



其次，我们发现执行框架采用了滚动截断窗口，随着历史记录增长，较早的操作会逐渐不可见。因此，GPT‑5.6 Sol 不仅无法记住过去的思考，也在逐渐忘记过去的操作。



执行框架的这两项特性——丢弃推理和滚动截断——共同解释了 GPT‑5.6 Sol 为何难以持续学习。



滚动截断有两个缺点。首先，模型会丢失较早的观察和操作。其次，**模型在大部分任务期间都要使用接近满载的上下文窗口**，这可能会略微影响表现。



图中左侧的Official ARC Harness一直在使用满载上下文窗口推理



![语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716236-86932199-92d8-48c5-b028-6637f4e34d9b.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_54%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



采用保留推理Reasoning-content和上下文压缩机制



![语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469717418-678d6cbc-022f-4ff6-b3b0-bfdc6c86b24f.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_17%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



reason保留有着不同的范围



- current_turn：保留当前 user turn 内、尤其是连续 tool calls 之间的 reasoning。

- all_turns：更早 user turns 的 compatible reasoning 也可以进入后续模型调用；GPT-5.6 默认支持并使用这一模式




<a id="7bda5510"></a>
## **turn和step的区别**



![语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716920-781e456d-a104-46c7-8120-84fb28217523.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_71%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



<a id="qwen"></a>
# Qwen



在Qwen3.6的Jinja中可以看到这段内容



当前step的Index > Last_user_query的时候就会保留当前turn中的reasoning_content的内容



但是开启了`preserve_thinking`后，将会一直保留reasoning_content



https://huggingface.co/Qwen/Qwen3.6-35B-A3B/blob/main/chat_template.jinja



![语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469716970-bd92f148-b2d2-4874-a714-6127bdf8cf63.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_56%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



但是在Qwen3.5中就看不到这个配置



![语雀原图](https://cdn.nlark.com/yuque/0/2026/png/39143682/1787469718409-d1ceca6e-d225-438e-ad33-5aa59deaccd8.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_52%2Ctext_5rC45rO954mb6YC8IHZ4K3d5emNhYw%3D%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)



<a id="25f9c7fa"></a>
# 总结



各大厂商Retain CoT的方式几乎一样，通过开启配置来启动
