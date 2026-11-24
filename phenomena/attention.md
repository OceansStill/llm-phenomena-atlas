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

某些 token 到中层才开始吸收较多注意力，几层后又消失。其持续层数和强度都可变。

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

**证据范围**　GPT-2 可出现整层现象。Llama-3-8B 的同一研究约有 500/1,024 个头接近秩一，分散在多层。

[Sanyal 等（TMLR 2026）](https://openreview.net/pdf?id=2zQn0bUoPf)

<a id="active-dormant"></a>

## Active–Dormant Attention Heads · 同一头随输入在活跃和休眠间切换

**观察对象**　注意力头 × 输入条件。

同一头在某些输入上读取与任务有关的信息，在另一些输入上把质量转向小 value 的 sink，写入显著减弱。

**怎么测**　对每个头按输入条件分组，同时看 sink 权重、头输出范数与消融后的目标分数。

**证据范围**　Bigram–Backcopy 玩具任务给出机制分析，作者也在预训练 Llama、OLMo 中观察到类似模式。一次休眠不能说明该头可以永久剪除。

[Guo 等（arXiv 2024）](https://arxiv.org/abs/2410.13835)

<a id="dispersion"></a>

## Attention Dispersion · 候选位置变多时注意力摊薄

**观察对象**　分布随可见 key 数的变化。

保持任务和目标 key 不变，加入更多竞争项后，原本尖锐的 softmax 选取会变得分散。

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
