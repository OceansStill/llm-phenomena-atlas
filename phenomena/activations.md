# 激活、通道与神经元

标量激活、整条 token 向量的范数、固定通道和单个模型权重处在不同层次。每一项都注明了比较的轴。

[全部现象](../README.md) · [论文索引](../papers.md) · [测量附录](../diagnostics.md)

<a id="massive-activations"></a>

## Massive Activations · 极少数标量激活异常大

**观察对象**　残差流的 token × 通道坐标。

极少数 token–通道坐标出现数量级远高于典型值的激活，部分位置跨样本相当稳定。

**怎么测**　每层保留绝对值最大的坐标及其相对中位数/RMS 的倍数。对照置零、替换为跨样本均值，以及等量随机坐标干预。

**证据范围**　Sun 等在多个预训练 LLM 中发现，部分巨值接近与输入无关的隐式偏置。只看全层均值容易漏掉它们。

[Mingjie Sun 等（COLM 2024）](https://arxiv.org/abs/2402.17762)

<a id="value-drains"></a>

## Value-State Drains · 汇聚位置的 value 向量范数偏小

**观察对象**　单头的 V 和加权写入。

吸引大量 attention 的位置，可以同时具有显著小于其他位置的 value 向量。

**怎么测**　配对查看同一位置的接收权重、value 范数，以及乘上注意力权重和输出投影后的写入范数。

**证据范围**　Guo 等结合玩具模型与预训练模型研究。sink 权重和 value 大小需要在同一个头里对齐。

[Guo 等（arXiv 2024）](https://arxiv.org/abs/2410.13835) · [Kobayashi 等（EMNLP 2020）](https://aclanthology.org/2020.emnlp-main.574/)

<a id="residual-peaks"></a>

## Residual-State Peaks · 个别 token 的残差向量范数尖峰

**观察对象**　残差流的 token 向量。

某些位置在残差流中的整条向量特别大，但同一 token 在不同头上的 V 仍可能很小。

**怎么测**　逐层画各位置的残差 L2 范数和极端位置相对普通 token 的比值，再比较归一化前后的数值。

**证据范围**　Guo 等图 1 在 Llama-3.1-8B-Base 的同一个 BOS 上展示了三种现象。它在第 24 层的多个头中吸收大量注意力，value 范数在各头、各层取平均后偏小，残差范数则在第 1–30 层持续偏大。这里按 token 位置找峰，Residual Sinks 则沿通道比较跨 token 的激活。

[Guo 等（arXiv 2024）](https://arxiv.org/abs/2410.13835) · [Mingjie Sun 等（COLM 2024）](https://arxiv.org/abs/2402.17762)

<a id="outlier-channels"></a>

## Outlier Features / Channels · 反复出现的离群特征维度

**观察对象**　激活矩阵的通道。

在所测的大模型中，同几个隐藏通道在许多 token 和多个层上反复出现大值，支配部分矩阵乘法的动态范围，使低比特量化更困难。

**怎么测**　逐通道记录峰值、超阈频率、跨 batch 坐标重合率，比较单独高精度保留这些维度前后的量化误差和困惑度。

**证据范围**　LLM.int8() 在所测模型的规模增长过程中观察到系统性离群特征。阈值和对应参数规模取决于模型与测量定义。

[Dettmers 等（NeurIPS 2022）](https://arxiv.org/abs/2208.07339)

<a id="residual-sinks"></a>

## Residual Sinks / Outlier-Driven Rescaling · 固定大通道调节归一化尺度

**观察对象**　RMSNorm 输入的通道与分母。

少数固定维度在多数 token 上持续偏大。它们抬高 RMS，使其余通道归一化后的幅度一起缩小。

**怎么测**　按 token 计算这些通道占平方和的比例，再测裁剪后的 RMS、普通通道幅度和语言建模损失。

**证据范围**　Qiu 等在多种训练设置中发现，保留归一化而直接裁剪异常值会损害表现。大激活可以具有调节功能。

[Qiu 等（COLM 2026）](https://proceedings.colm.cc/paper_files/paper/2026/hash/19f10adb6749b0c9f1ff7610bd01d44d-Abstract-Conference.html)

<a id="qk-outliers"></a>

## Massive Values in Q and K · Q/K 低频旋转维度出现条带状巨值

**观察对象**　Q/K 的 RoPE 频率对。

在 Gemma2-9B 等模型中，Q、K 的极值集中在少数低频旋转维度。V 缺少类似的固定条带。

**怎么测**　按 head 和 RoPE 频率对排列 Q/K 激活，检查极值密度。对低频极值做定向干预，再比较依赖上下文的任务与参数知识任务。

**证据范围**　Jin 等干预所测 RoPE 模型的 Q/K 巨值后，依赖上下文线索的检索、文本理解等任务受损更明显，参数知识检索受影响较小。这里的 massive values 指 Q、K 中的数值大小。

[Jin 等（ICML 2025）](https://proceedings.mlr.press/v267/jin25f.html)

<a id="super-weights"></a>

## Super Weights / Super Activations · 单个权重对模型行为有异常大的影响

**观察对象**　单个参数及其下游激活。

在部分模型中，移除极少数特定权重、甚至一个标量，就足以破坏生成。相应权重可引发稀有的大激活。

**怎么测**　定位精确权重坐标，对照移除该权重、同层等幅值权重及随机权重，再看困惑度与生成质量。

**证据范围**　Yu 等给出了多种开放模型的坐标。权重的绝对值排名和删除后的因果影响是两个不同统计量。

[Yu 等（arXiv 2024）](https://arxiv.org/abs/2411.07191)

<a id="swiglu"></a>

## SwiGLU Weight Alignment / Outlier Amplification · 门控投影对齐放大离群激活

**观察对象**　同一 MLP 单元的 gate 和 up 支路。

长时间训练后，部分 gate 与 up 投影逐渐对齐。同一特征同时触发两条支路，相乘后形成更大的输出。

**怎么测**　跨 checkpoint 跟踪成对权重余弦、两支路共同出现大值的比率和乘积尾部分位数。

**证据范围**　Fishman 等将此现象和长程 FP8 训练失稳联系起来。短训练的峰值统计可能捕捉不到这一过程。

[Fishman 等（ICLR 2025）](https://proceedings.iclr.cc/paper_files/paper/2025/hash/f48b5133e89854a9e97cc22a6db83f25-Abstract-Conference.html)

<a id="lazy-neurons"></a>

## Lazy Neuron Phenomenon / Activation Sparsity · 每个输入只激活少数前馈单元

**观察对象**　ReLU 后的 MLP 中间层。

在采用 ReLU 的模型中，对一个给定 token，通常只有少数 MLP 中间单元输出非零。不同 token 可以激活不同的单元集合，因此单元的跨样本触发率也需单独统计。

**怎么测**　同时报告每个 token 的非零比例与每个神经元跨 token 的激活频率，注明激活函数。

**证据范围**　原文示例包括 T5-Base。SwiGLU 的输出通常连续，跨架构比较应另报明确的近零阈值。

[Li 等（ICLR 2023）](https://arxiv.org/abs/2210.06313)

<a id="weakening"></a>

## Weakening Neurons · 神经元削弱其读到的特征

**观察对象**　GLU 单元的读、写权重方向。

部分神经元的读取和写入方向强负相关，在相应门值下能减弱它检测到的残差方向。

**怎么测**　计算读取和写入向量的余弦，结合 gate 符号、激活频率与干预后目标 logit 的变化。

**证据范围**　作者在 9 个 LLM 中看到这类单元更集中于深层，数量少而经常激活。有效写入仍取决于输入和门值。

[Gerstner 等（EMNLP 2026）](https://aclanthology.org/2026.emnlp-main.455/)

<a id="entropy-neurons"></a>

## Entropy Neurons · 借助残差范数调节预测置信度

**观察对象**　末端 MLP 写入与最终归一化。

某些神经元写向输出投影不敏感的方向，能增大残差范数。最终归一化因此缩小整组 logits，并提高下一词的预测熵。

**怎么测**　同时比较写入与输出投影的对齐、最终归一化尺度、消融前后下一词熵，分离直接与间接效应。

**证据范围**　Stolfo 等在最高 7B 的模型中观察到这类神经元。这里的熵是词表上的输出熵。

[Stolfo 等（NeurIPS 2024）](https://proceedings.neurips.cc/paper_files/paper/2024/hash/e21955c93dede886af1d0d362c756757-Abstract-Conference.html)

<a id="layernorm-outliers"></a>

## LayerNorm Outlier Dimensions · 多个 LayerNorm 在相同坐标上出现异常参数

**观察对象**　各层 LayerNorm 的仿射缩放 γ 与偏置 β。

在 BERT 类模型中，若干固定坐标在多层 LayerNorm 中具有异常的缩放或偏置。跨层禁用极少数这样的坐标，就能显著破坏模型预测。

**怎么测**　分别对每层的 γ、β 做坐标排序，找出跨层重复的离群坐标。把所选坐标的缩放和偏置同时置零，与禁用同等数量随机坐标时的掩码语言建模损失比较。

**证据范围**　Kovaleva 等在 BERT 等模型上做了跨层参数干预。这里直接检查模型参数。应用于只有缩放项的 RMSNorm 时，需要按该架构重新验证。

[Kovaleva 等（Findings of ACL-IJCNLP 2021）](https://aclanthology.org/2021.findings-acl.300/)

相关条目　[隐藏状态的离群通道](activations.md#outlier-channels)

<a id="frequency-neurons"></a>

## Token Frequency Neurons · 沿词频方向调节下一词概率的神经元

**观察对象**　FFN 神经元对整个词表 logits 的写入向量。

某些神经元对每个候选词 logit 的影响随该词的对数频率变化。改变它的激活，会使预测向语料的一元词频分布靠近或远离。

**怎么测**　对单个神经元实施消融或改变激活，计算每个词的 logit 变化量。去掉全词表共享的偏移后，与语料的对数词频比较，并按上下文复测。

**证据范围**　Stolfo 等在同一研究中辨认出词频神经元和熵神经元。前者按词频有选择地调整候选词，后者主要通过末端 LayerNorm 的尺度调节整组 logits。

[Stolfo 等（NeurIPS 2024）](https://proceedings.neurips.cc/paper_files/paper/2024/hash/e21955c93dede886af1d0d362c756757-Abstract-Conference.html)

相关条目　[熵神经元](activations.md#entropy-neurons)

<a id="dead-neurons"></a>

## Dead Neurons · 在整个观测语料上始终未激活的神经元

**观察对象**　固定 FFN 神经元跨样本、跨 token 的激活次数。

在采用 ReLU 的 OPT 中，部分神经元在大规模、多样化文本上一次也没有激活，尤其集中在早期层。

**怎么测**　逐神经元累计 ReLU 输出严格大于零的次数，逐层报告次数为零的比例，并记录语料域和 token 数。扩大样本，检查原先标为零的神经元是否被激活。

**证据范围**　Voita 等比较了 OPT-125M 至 OPT-66B。有限语料只能给出观测期内的零激活。原目录的 Lazy Neuron Phenomenon 衡量的是给定一个 token 时有多少神经元活跃。

[Voita 等（Findings of ACL 2024）](https://aclanthology.org/2024.findings-acl.75/)

相关条目　[单个 token 的 FFN 激活稀疏性](activations.md#lazy-neurons)
