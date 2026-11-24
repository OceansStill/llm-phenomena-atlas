# 注意力与位置

注意力权重的行对应 query，列对应可见的 key。单行熵、跨行的列集中和整张矩阵的谱回答三个不同的问题。

[全部现象](../README.md) · [论文索引](../papers.md) · [测量附录](../diagnostics.md)

<a id="attention-sinks"></a>

## Attention Sinks · 注意力汇聚到少数位置

**观察对象**　单头注意力列。

大量后续 query 持续把注意力分给开头等少数位置，替换这些位置的词义也可能保留现象。

**怎么测**　按位置统计可见 query 分给它的平均权重，与同长度基线比较。滚动 KV 缓存时对照保留或移除开头位置的困惑度。

**证据范围**　StreamingLLM 在多个解码器上验证了保留初始位置对流式推理的作用。高权重位置的实际写入还受 value 大小影响。

[Xiao 等（ICLR 2024）](https://arxiv.org/abs/2309.17453) · [Guo 等（arXiv 2024）](https://arxiv.org/abs/2410.13835) · [Gu 等（ICLR 2025）](https://arxiv.org/abs/2410.10781)

<a id="secondary-sinks"></a>

## Secondary Attention Sinks · 中间层出现的次级注意力汇

**观察对象**　位置 × 层。

某些 token 到中间层才开始持续吸收注意力。它们分到的权重通常低于主 sink，持续层数各异，有些几层后就消失。

**怎么测**　逐层追踪同一位置的 sink 分数、首次出现层和持续层数，并与主 sink 对照。

**证据范围**　作者比较 11 个模型家族，并把部分次级 sink 的形成定位到中层 MLP。单凭某个 token 不是 BOS，无法判定它是次级 sink。

[Wong 等（arXiv 2025）](https://arxiv.org/abs/2512.22213)

<a id="attention-entropy"></a>

## Attention Entropy Collapse · 注意力行熵坍缩

**观察对象**　每个 query 上的注意力分布。

训练期间，部分头的注意力分布突然变得极尖，质量集中到极少数 key。

**怎么测**　只在因果掩码允许的 key 上求行熵，按头、层和训练步画分位数，并对齐 loss 曲线。首行天然只有一个可见位置。

**证据范围**　Zhai 等给出谱范数与熵的分析，并在多种 Transformer 任务中研究训练不稳定性。单个检索头在正确证据上形成尖峰也能是有效行为。

[Zhai 等（ICML 2023）](https://proceedings.mlr.press/v202/zhai23a.html)

<a id="attention-collapse"></a>

## Attention Collapse / Lazy Layers · 注意力矩阵近秩一

**观察对象**　softmax 后单头的 T × T 矩阵。

不同 query 的注意力向量近似共享一个主方向。论文把一层内所有头都接近秩一的情形称为 lazy layer。

**怎么测**　逐头统计达到既定奇异值能量占比所需的近似秩，并查看各头的分布。这里的矩阵是注意力权重 A。

**证据范围**　在 GPT-2 的某些深层，层内全部头的注意力矩阵都接近秩一。按论文的近似秩指标，Llama-3-8B 的 1,024 个头中约有 500 个接近秩一，分布在多个层。

[Sanyal 等（TMLR 2026）](https://openreview.net/pdf?id=2zQn0bUoPf)

<a id="active-dormant"></a>

## Active–Dormant Attention Heads · 同一头随输入在活跃和休眠间切换

**观察对象**　注意力头 × 输入条件。

同一头在某些输入上读取与任务有关的信息，在另一些输入上把质量转向小 value 的 sink，写入显著减弱。

**怎么测**　对每个头按输入条件分组，同时看 sink 权重、头输出范数与消融后的目标分数。

**证据范围**　Guo 等从 Bigram–Backcopy 玩具任务识别出这种机制，也在预训练 Llama 和 OLMo 中观察到类似模式。图 8 的 Llama-2-7B-Base 在同一个头 L16H25 上表现出输入域差异。它在 GitHub 代码上活跃，在 Wikipedia 文本上把注意力转向 sink。将这个头的 value 置零，对代码的交叉熵损失影响更大。

[Guo 等（arXiv 2024）](https://arxiv.org/abs/2410.13835)

<a id="dispersion"></a>

## Attention Dispersion · 候选位置变多时注意力摊薄

**观察对象**　分布随可见 key 数的变化。

当目标 key 与竞争项的打分差保持有界时，即使目标始终得分最高，增加候选数也会摊薄它的 softmax 权重。

**怎么测**　控制目标位置、干扰项分布和 query，逐步增加候选数，记录目标权重、最大权重和准确率。

**证据范围**　论文分析 sharp size generalisation，并用如选出最大值的任务检验。迁移到长文本时需控制位置、分数尺度和任务难度。

[Veličković 等（ICML 2025）](https://proceedings.mlr.press/v267/velickovic25a.html)

<a id="bf16-rope"></a>

## RoPE Relative-Position Breakdown in BF16 · BF16 下相对位置平移不变性失效

**观察对象**　旋转后的 Q/K 点积与注意力分数。

相同 token 序列整体平移 position IDs，本应不变的相对位置分数产生偏差，在长上下文中更明显。

**怎么测**　保持 token、mask 和相对距离一致，平移全部 position IDs。用高精度对照，并分别查看首 token 列与其他位置。

**证据范围**　论文在所测 BF16 计算实现中观察到首 token 的显著贡献。复现时要记录旋转角、旋转运算及点积各自的精度。

[Wang 等（TMLR 2025）](https://arxiv.org/abs/2411.13476)

<a id="inter-head-predictability"></a>

## Inter-head Linear Predictability · 少数同层注意力头可以预测其他头

**观察对象**　同一层、同一个 token 的不同注意力头的 Q、K 或 V 向量。

给定少数参考头，用线性变换就能重建另一个头在同一 token 上的部分信号。模型预训练过程中，这种可预测性逐渐增强。

**怎么测**　在一批文本上用参考头的 K 拟合目标头的 K，换留出文本报告每个目标头的 R²。对 Q、V 分开重复。使用 GQA 时直接提取未展开的 KV 头，并与随机初始化的同架构对照。

**证据范围**　Shaikh 等研究 Llama、Falcon、OLMo 和 Qwen。在论文的 C4 实验中，用五个参考头预测 K 的平均 R² 约为 0.76。将预测值实际用于推理时，各模型掉分幅度不同，重建 V 通常带来更大损失。

[Shaikh 等（arXiv 2026）](https://arxiv.org/abs/2603.13314)

<a id="copy-suppression"></a>

## Copy Suppression · 注意力头压低从上文照抄的候选词

**观察对象**　较晚注意力头对候选 token 的 logit 写入。

早期组件已经预测某个词，而且这个词出现在前文。部分后层头会关注它的先前位置，再压低它作为下一词的 logit。

**怎么测**　在候选词出现过和未出现过的配对前缀上，记录早期预测、该头对先前位置的注意力以及它写向候选词 logit 的有符号贡献。消融该头，检查复制概率与预测损失怎样变化。

**证据范围**　McDougall 等在 GPT-2 Small 的 L10H7 中追踪了这条路径。该头抑制部分过度自信的复制预测，在原模型的正常前向传播中改善了校准。

[McDougall 等（BlackboxNLP 2024）](https://aclanthology.org/2024.blackboxnlp-1.22/)

<a id="lost-in-the-middle"></a>

## U-Shaped Positional Attention Bias / Lost in the Middle · 位于上下文中段的证据更容易被忽略

**观察对象**　证据所在位置、输出 query 对证据的注意力，以及同一题的答案。

把同一段证据从输入开头移到中间，再移到末尾，部分长上下文模型的检索或问答成绩呈 U 形。注意力分析也观察到两端位置获得额外权重。

**怎么测**　固定问题、证据、总长度和干扰文档，只轮换证据位置并平衡干扰项次序。逐位置报告正确率，再统计作答时分给证据 span 的注意力，控制 span 长度。

**证据范围**　Liu 等在多文档问答和键值检索中测试了位置效应。Hsieh 等进一步测量 U 形注意力偏置，并干预该偏置来检验它对长上下文利用的贡献。

[Liu 等（TACL 2024）](https://aclanthology.org/2024.tacl-1.9/) · [Hsieh 等（Findings of ACL 2024）](https://aclanthology.org/2024.findings-acl.890/)
