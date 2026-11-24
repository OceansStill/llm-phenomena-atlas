# 深度、残差与训练动力学

层号、训练步与 token 位置是三条不同的轴。梯度在某个位置集中、沿深度失衡和在某一训练步突增，分别对应不同条目。

[全部现象](../README.md) · [论文索引](../papers.md) · [测量附录](../diagnostics.md)

<a id="deep-ineffectiveness"></a>

## Deep-Layer Ineffectiveness / Layer Redundancy · 删除部分连续深层后仍能保留任务表现

**观察对象**　连续层块的实际功能贡献。

某些开放模型中，可以找出较深的一段连续层，删去并做少量修复训练后，部分问答基准仍保留较多表现。

**怎么测**　扫描连续层块的输入–输出角距离，再实际删除。分别报告原模型、仅剪枝及等预算修复后的困惑度和多种任务结果。

**证据范围**　Gromov 等的结果依赖模型、任务、删除比例与修复训练。层可剪性与训练时残差机制是两类证据。

[Gromov 等（ICLR 2025）](https://arxiv.org/abs/2403.17887)

<a id="curse-of-depth"></a>

## Curse of Depth · 深层残差更新相对输入变弱

**观察对象**　Pre-LN 残差流与块的局部响应。

在论文分析的 Pre-LN 配置中，残差流方差随深度增大，较深块趋近恒等映射，新增计算的相对影响减小。

**怎么测**　逐层记录残差 RMS、分支更新与输入范数之比，并用跳层后的输出变化核对。

**证据范围**　Wenfang Sun 等研究了 Llama、Mistral、DeepSeek、Qwen 等模型族，并通过训练不同尺度的模型检验归一化缩放。

[Wenfang Sun 等（NeurIPS 2025）](https://arxiv.org/abs/2502.05795)

<a id="depth-routing-collapse"></a>

## Routing Collapse across Depth · 累积状态来源的跨层路由趋平

**观察对象**　对历史来源的 softmax 分布。

当每个候选都是从开头累加至某层的完整状态时，相邻候选共享大量内容。Delta Attention Residuals 的基线中，深层路由权重区分度降低。

**怎么测**　按层报告候选数、最大权重、相对均匀基线的熵和候选间相似度。分开比较累积状态、子层写入及块内写入和。

**证据范围**　约 0.2 是该文 28 层、Qwen3-0.6B 架构累积状态基线的深层最大权重指标。Kimi 原版 Full 读取子层输出，Block 读取块内输出和（原文式 3、5）。

[Luo 等（arXiv 2026）](https://arxiv.org/abs/2605.18855) · [Kimi Team（2026 技术报告）](https://arxiv.org/abs/2603.15031)

<a id="residual-erasure"></a>

## Residual Stream Erasure · 后层抵消早期头的写入

**观察对象**　正常前向传播中残差里的特定方向。

早层注意力头写入一个方向，后层的特定头随后将这个方向抵消，早层的直接 logit 贡献因而可能高估其最终影响。

**怎么测**　投影早层写入与后续各层写入到同一方向，再干预候选擦除头，查看末端读出是否恢复。

**证据范围**　Janiak 等在四层研究模型 GELU-4L 中追踪到这条因果链。

[Janiak 等（BlackboxNLP 2024）](https://aclanthology.org/2024.blackboxnlp-1.15/)

<a id="hydra-effect"></a>

## Hydra Effect / Self-Repair · 消融后其他组件补偿

**观察对象**　组件受干预后的下游响应。

消融一个头或一层后，后续组件改变写入，部分抵消这次干预对最终输出的影响。

**怎么测**　对照正常前向和消融前向，分别记录直接贡献、下游贡献变化与总 logit 变化。

**证据范围**　多篇语言模型研究考察了不同形式和限度的补偿。应区分干预后发生的补偿和正常前向中的擦除。

[McGrath 等（arXiv 2023）](https://arxiv.org/abs/2307.15771) · [Rushing 与 Nanda（ICML 2024）](https://proceedings.mlr.press/v235/rushing24a.html)

<a id="premature-attention"></a>

## Premature Upper-Layer Attention Specialization · 上层注意力过早特化

**观察对象**　上层 Q/K 与下层表征的训练时间顺序。

下层匹配与复制特征还在变化时，上层 Q/K 已经形成尖锐并被预测依赖的注意力模式。

**怎么测**　沿 checkpoint 对齐上层注意力熵、Q/K logits 与下层复制行为。对照仅在早期放慢上层 Q/K 的训练。

**证据范围**　论文在 270M GPT 风格模型中看到最终困惑度改善。匹配的门控 FFN 对照中，干预收益明显减小。

[Zhu 等（arXiv 2026）](https://arxiv.org/abs/2605.10504)

<a id="gradient-mismatch"></a>

## Pre-LN Gradient Magnitude Mismatch · 浅深层参数梯度失配

**观察对象**　同一训练步沿深度的参数梯度。

在部分 Pre-LN 预训练设置，浅层梯度比深层大得多。持续存在的深度落差与偶发梯度尖峰可分别测量。

**怎么测**　同一步内，按同类型矩阵比较梯度 RMS 或每参数范数，并分开记录裁剪前梯度、权重 RMS 与实际更新。

**证据范围**　NormFormer 的预训练实验比较了不同层的梯度尺度及增加归一化后的变化。

[Shleifer 等（arXiv 2021）](https://arxiv.org/abs/2110.09456)

<a id="logit-growth"></a>

## Attention Logit Growth · 注意力打分随训练异常增大

**观察对象**　softmax 前 QK 打分。

训练过程中，部分头在 softmax 之前的打分 $QK^\top/\sqrt{d_k}$ 尺度不断增大。若行内分差也被放大，分布就容易饱和，行熵下降，并可能出现训练不稳定。

**怎么测**　在有效位置追踪每头最大值、行内极差、去行均值后的 RMS 和相应行熵。统一加到整行的常数不会改变 softmax。

**证据范围**　Wortsman 等在较小模型的高学习率条件下复现了部分大规模训练不稳定性。

[Wortsman 等（ICLR 2024）](https://arxiv.org/abs/2309.14322) · [Zhai 等（ICML 2023）](https://proceedings.mlr.press/v202/zhai23a.html)

<a id="gradient-spikes"></a>

## Gradient Spikes · 单步或少数坐标梯度突增

**观察对象**　训练时间 × 参数坐标。

少数训练步或特定参数的梯度显著超过自身历史尺度，并可能持续影响优化器的动量估计。

**怎么测**　记录裁剪前每参数梯度与其历史尺度之比、异常比例和训练损失。再看全局范数，避免少数坐标被均值掩盖。

**证据范围**　SPAM 对多个 LLM 训练设置进行了实证分析。梯度突增与 loss spike 可分别标记，再检查时间关系。

[Huang 等（ICLR 2025）](https://proceedings.iclr.cc/paper_files/paper/2025/hash/7a70ad3d9c704fb9b81b5c69eda722dc-Abstract-Conference.html) · [Wortsman 等（ICLR 2024）](https://arxiv.org/abs/2309.14322)

<a id="gradient-sinks"></a>

## Gradient Sinks · 反向梯度集中到少数 token 位置

**观察对象**　反向传播中的位置梯度。

在因果注意力下，前向汇聚的 token 也可能接收多个后续位置累积回传的梯度。大残差范数又能经 RMSNorm 调节局部梯度压力。

**怎么测**　固定损失归约，按 token 比较 value 路径与归一化前后梯度范数。使用只改反向路径的对照，检查前向 sink 是否仍保留。

**证据范围**　Chen 等用理论、训练实验和只改反向路径的干预检验这一机制。论文图 2 中，首 token 在 K 和 V 路径上的梯度范数跨多个检查点出现尖峰，V 路径尤其明显。比较位置即可识别这种集中，比较同一参数在不同训练步的变化则用于识别 Gradient Spikes。

[Chen 等（arXiv 2026）](https://arxiv.org/abs/2603.17771)
