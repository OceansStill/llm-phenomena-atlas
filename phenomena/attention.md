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

<a id="value-non-mixing"></a>

## Value-Non-Mixing · 注意力读到的 value 都与当前位置相同

**观察对象**　单个注意力头在位置 i 读取的 value 向量，以及输出投影前的加权和。

如果每个获得非零注意力的位置 j 都满足 vⱼ = vᵢ，那么无论该行注意力多么分散，读出的加权和都等于 vᵢ。

**怎么测**　可按注意力权重对各 vⱼ 到 vᵢ 的平方距离求和。这个量为零时满足原文的严格定义。用省略 BOS 的全重复 token 序列作对照，再在开头、第二或第三个位置插入异类 token，逐位置记录该量和隐藏状态范数。

上面的加权平方距离是依定义给出的复测指标。直接比较加权和与 vᵢ 可能漏掉方向相反的偏差，因为它们在求和时可以抵消。

**证据范围**　Kiya 等在第 4.3 节式（4）定义了这一状态。图 4 的 Llama-3.2-3B 实验中，完全重复的输入让多个位置分到注意力，各位置仍出现巨大激活。该实验隔离了 value 相同这一条件。

[Kiya 等（EMNLP 2026）](https://aclanthology.org/2026.emnlp-main.1366/)

相关条目　[注意力汇聚的位置](attention.md#attention-sinks) · [value 范数异常小](activations.md#value-drains) · [注意力加权值与实际写入](../diagnostics.md#weighted-values)

<a id="rope-inversion-aliasing"></a>

## RoPE Score Inversion and Aliasing · RoPE 打分反转与碰撞

**观察对象**　固定旋转前的 query 和 key 后，单头在相对距离 m 处的 softmax 前打分。

本条目把 Du 等分别命名的 Position Inversion、Position Aliasing、Token Inversion 和 Token Aliasing 合并为一项。前两种比较同一个 key 在不同距离的分数，后两种比较两个 key 的分数。

下表用 $S_k(m)$ 表示固定查询对键 $k$ 在相对距离 $m$ 处的打分。

| 原文术语 | 固定什么、改变什么 | 判据 |
| --- | --- | --- |
| Position Inversion | 键相同，从较近的 $m_1$ 换到较远的 $m_2$ | $S_k(m_1) < S_k(m_2)$。按论文的采样，从距离范围的前、后半段各取一个位置。 |
| Position Aliasing | 键相同，比较两个不同距离 | $S_k(m_1) = S_k(m_2)$。 |
| Token Inversion | 键为 $k_1,k_2$，两者总在同一距离比较 | 原先 $S_{k_1}(0) > S_{k_2}(0)$，换到距离 $m$ 后有 $S_{k_1}(m) < S_{k_2}(m)$。 |
| Token Aliasing | 距离相同，比较两个不同的键 | $S_{k_1}(m) = S_{k_2}(m)$。 |

**怎么测**　从同一注意力头取旋转前的 q、k，固定向量并扫描相对距离。按四种判据分别计数严格反转和分数相等的配对，记录 RoPE 基数、距离范围与计算精度。用更高精度重算碰撞的配对，检查舍入对结果的影响。

**证据范围**　Du 等 v1 第 3.1、3.2、4.1、4.2 节和表 1 给出四种定义，图 4、5、7、8 用 Llama-3.1-8B 第 0 层第 0 头示例。碰撞指选定精度下的标量分数相等。论文对打分的分析本身不能给出整模型检索的错误率。

[Du 等（arXiv 2026，v1）](https://arxiv.org/html/2605.15514v1)

相关条目　[保持相对距离、整体平移 position IDs 的 BF16 实验](attention.md#bf16-rope)

<a id="retrieval-heads"></a>

## Retrieval Heads · 从远处证据定位并复制答案的注意力头

**观察对象**　逐头观测生成答案时最大注意力的位置、该位置的 token 与当步输出。

把同一段证据放到长上下文的不同位置，少数头会跟随证据，并在生成答案时指向包含相应 token 的位置。Wu 等还在使用短上下文预训练的基础模型中观察到了这些头。

**怎么测**　将证据插入不同位置。若最大注意力落在证据内，且该位置的 token 与生成的 token 相同，记作命中。按证据长度归一化，逐头汇总检索分数。比较屏蔽高分头与屏蔽同等数量随机非检索头后的正确率。

**同页概念 · Streaming Heads**　DuoAttention 为这类头只保留开头和最近窗口的 KV，给需要访问远处历史的头保留完整缓存。检验一个头是否依赖远处历史，可以比较限制其可见窗口前后的输出。

**证据范围**　Wu 等按这套复制判据，在所测模型中识别出不到 5% 的头，屏蔽它们会损害检索。Xiao 等的 DuoAttention 按每个头对完整 KV 历史的需求区分 Retrieval Heads 和 Streaming Heads。Wu 等的 5% 只对应前一种判据。

[Wu 等（ICLR 2025）](https://proceedings.iclr.cc/paper_files/paper/2025/hash/9b77f07301b1ef1fe810aae96c12cb7b-Abstract-Conference.html) · [Xiao 等（ICLR 2025）](https://proceedings.iclr.cc/paper_files/paper/2025/hash/5c1ddd2e59df46fd2aa85c833b1b36ed-Abstract-Conference.html)

相关条目　[证据位置对长上下文作答的影响](attention.md#lost-in-the-middle) · [开头位置吸收注意力](attention.md#attention-sinks)
