# 表示几何与信息传播

定好矩阵的行、列和取样方式，再解释谱。单个输入的 token 表示、跨样本语境表示和注意力权重有不同的几何意义。

[全部现象](../README.md) · [论文索引](../papers.md) · [测量附录](../diagnostics.md)

<a id="token-rank"></a>

## Rank Collapse / Token Uniformity · 不同 token 的表示趋同

**观察对象**　层输出 H 的 token × 隐藏维。

在纯注意力网络的理论设置中，深度增加使各 token 的表示趋同，输出趋向秩一。初始化时的表示坍缩还可能使 Q/K 参数梯度消失。

**怎么测**　逐层比较 H 的奇异值谱、第一奇异值的能量占比，以及去除逐列均值后剩余能量占比。记录 padding 和 BOS 处理。

**证据范围**　Dong 等的双指数结论针对去除残差与 MLP 的纯注意力网络。Noci 等的初始化分析说明了坍缩与梯度传播的联系。

[Dong 等（ICML 2021）](https://proceedings.mlr.press/v139/dong21a.html) · [Noci 等（NeurIPS 2022）](https://arxiv.org/abs/2206.03126)

<a id="rank-in-width"></a>

## Rank Collapse in Width · 上下文增长诱发的谱退化

**观察对象**　随机初始化下的注意力谱与表示传播。

在论文分析的随机注意力模型中，即使固定深度，增加序列长度也可能引起退化。注意力矩阵前两个奇异值间的谱间隙是机制之一。

**怎么测**　固定深度，扫描序列长度，测注意力矩阵的前两个奇异值、间隙以及输出表示的谱。另以固定长度扫描深度作对照。

**证据范围**　Nait Saada 等使用随机矩阵理论研究初始化。文中的 width 现象随上下文长度出现，向训练后 LLM 推广需要另行测量。

[Nait Saada 等（ICML 2025）](https://proceedings.mlr.press/v267/nait-saada25a.html)

<a id="compression-valleys"></a>

## Compression Valleys · 中间层的表示谱熵形成谷底

**观察对象**　token 表示矩阵的奇异值能量分布。

部分模型的中间层把大部分能量集中于少数方向，表示矩阵的谱熵先降后升，并可与 BOS 巨值及注意力汇同时出现。

**怎么测**　用归一化的奇异值平方计算谱熵，画深度曲线，同时追踪 BOS 范数及其接收权重。比较保留与移除 BOS 后的谱。

**证据范围**　论文在 410M–120B 的模型中检验这种联系。只要一个 BOS 的范数足够大，它就能在保留各行范数的表示矩阵中主导第一奇异值，显著压低谱熵。此时其余 token 仍可能彼此可区分。

[Queipo-de-Llano 等（ICLR 2026）](https://arxiv.org/abs/2510.06477)

<a id="anisotropy"></a>

## Representation Anisotropy · 表示方向偏向共同子空间

**观察对象**　跨 token 或语境的方向分布。

从不同语境抽取的向量偏向某些共同方向，随机配对的余弦基线可显著偏离各向同性空间。只要部分方向占据大部分能量，一个满秩表示矩阵也可高度各向异性。

**怎么测**　按层报告独立语境向量之间的余弦基线，并报告保留和去除均值后的主成分解释率。注明取词、分词和采样方法。

**证据范围**　原文比较 BERT、ELMo 与 GPT-2 的上下文化表示。各向异性本身不决定下游任务表现。

[Ethayarajh（EMNLP-IJCNLP 2019）](https://aclanthology.org/D19-1006/)

<a id="rogue-dimensions"></a>

## Rogue Dimensions · 少数坐标主导相似度度量

**观察对象**　余弦、点积或距离中的逐维贡献。

少数坐标对相似度分数贡献特别大，可掩盖其余维度中与词义有关的结构。

**怎么测**　将点积分解到各坐标，再做逐维标准化或排除候选维度，比较词义相似度排序及其变化。

**证据范围**　Timkey 与 van Schijndel 观察到，往往仅 1–3 个维度就能主导相似度。图 1 中，GPT-2 的 3 维和 XLNet 的 1 维主导余弦相似度，而这些维度对所测语言建模输出分布的影响很小。

[Timkey 与 van Schijndel（EMNLP 2021）](https://arxiv.org/abs/2109.04404)

<a id="oversquashing"></a>

## Information Over-Squashing · 不同前缀在末位表示中变得难以区分

**观察对象**　末位置对输入 token 的敏感度。

某些不同前缀在最后一个位置产生任意接近的表示，关键 token 的差异传到末位时已很弱，计数或复制可因此失败。

**怎么测**　构造成对输入，只改一个决定答案的 token。按位置、长度和精度比较末位隐藏差异、答案 logit 差及准确率。

**证据范围**　原文结合 decoder-only 的理论分析与实验证据。长上下文任务要保证修改后的参考答案与扰动对应。

[Barbero 等（NeurIPS 2024）](https://arxiv.org/abs/2406.04267)

<a id="subspace-collapse"></a>

## Subspace Collapse in Next-Token Prediction · 后继词集合相同的语境在子空间内趋同

**观察对象**　不同语境在指定子空间的投影。

若两种语境拥有相同的可能后继词集合，即使各词频率不同，其表示在特定子空间的投影也会趋同。

**怎么测**　按后继词支持集配对语境，区分投影距离、完整表示距离和词频差异。用支持集不同的语境作为对照。

**证据范围**　该结果建立于简化的理论设置，并由合成数据和小规模真实语言实验支持。

[Zhao 等（2024）](https://arxiv.org/abs/2408.15417)

<a id="attention-output-collapse"></a>

## Dimensional Collapse in Attention Outputs · 多头注意力写入残差流的向量有效维度偏低

**观察对象**　所有注意力头合并、经过输出投影后、与残差相加前的向量。

从许多 token 收集同层注意力块的写入，奇异值集中在部分方向。不同头的输出子空间有重叠，合并后的写入主要沿其中一部分方向变化。

**怎么测**　对各 token 的写入堆叠成矩阵并按列中心化。以奇异值占总和的比例 p 计算有效秩 exp(−Σ p log p)，再除以隐藏宽度 d。在相同 token 上对照 MLP 写入与残差流。

**证据范围**　Wang 等在多种模型和数据集上测得注意力写入的有效秩约为隐藏宽度的 60%，并考察了对稀疏自编码器的影响。该百分比指有效秩与宽度之比。

[Wang 等（ICML 2026）](https://proceedings.mlr.press/v306/wang26bh.html)

相关条目　[token 表示的秩坍缩](representations.md#token-rank) · [单头注意力矩阵的坍缩](attention.md#attention-collapse)

<a id="token-embedding-degeneration"></a>

## Token Embedding Degeneration · 词表输出嵌入向少数公共方向挤压

**观察对象**　输出词嵌入矩阵中按词表 ID 排列的行向量。

训练中，不同词的输出嵌入逐渐挤进狭窄的锥形区域。对稀有词嵌入的部分梯度作干预，可以同时改变高频词和稀有词的几何分布。

**怎么测**　在训练检查点按语料词频把词表分组，跟踪原始词向量间的余弦、各组各向同性及生成时稀有词的使用量。将干预稀有词梯度的实验与相同训练配置对照。

**证据范围**　Yu 等在语言建模实验中追踪到一个关键梯度分量，它将稀有词嵌入推离目标为非稀有词的上下文向量。这里每个观测点对应一个词表项，适合和上下文化隐藏表示的各向异性分开测量。

[Yu 等（ACL 2022）](https://aclanthology.org/2022.acl-long.3/)

相关条目　[上下文化表示的各向异性](representations.md#anisotropy)
