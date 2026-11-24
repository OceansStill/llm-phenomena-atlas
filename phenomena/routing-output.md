# 专家路由与词表输出

稀疏专家模型中可以分别观察表示聚簇、同一输入在训练中改换专家、以及容量限制引起的位置偏置。输出层还存在独立的词表几何约束。

[全部现象](../README.md) · [论文索引](../papers.md) · [测量附录](../diagnostics.md)

<a id="moe-collapse"></a>

## Representation Collapse in Sparse MoE · 同一专家内的 token 表示聚簇

**观察对象**　token 与路由专家中心。

路由训练使 token 表示向各自专家中心聚集，分到同一专家的不同 token 可能越来越相似。

**怎么测**　记录专家内离散度、到中心的距离与不同专家间距离，同时报告专家负载。均衡负载和专家内表示退化可以同时发生。

**证据范围**　Chi 等在多语种语言模型预训练与下游任务中研究该趋势。

[Chi 等（NeurIPS 2022）](https://proceedings.neurips.cc/paper_files/paper/2022/hash/df4f371f1f89ec8ba5014b3310578048-Abstract.html)

<a id="routing-fluctuation"></a>

## Routing Fluctuation · 相同输入沿训练过程改换专家

**观察对象**　固定输入跨 checkpoint 的专家选择。

同一个输入在不同训练阶段被送往不同专家，训练信号分散于多个专家，而推理只使用最后选中的专家。

**怎么测**　固定输入、position 和层，计算相邻 checkpoint 的专家切换率。在 top-k 路由中另外报告集合重合率。

**证据范围**　StableMoE 指出，在其单专家路由设置中，同一输入的训练信号分散给多个专家，推理时只使用其中一个，这会降低样本效率。外推到 top-k 路由时需要注明专家选择规则。

[Dai 等（ACL 2022）](https://aclanthology.org/2022.acl-long.489/)

<a id="drop-towards-end"></a>

## Drop-towards-the-End · 靠后 token 更容易被专家丢弃

**观察对象**　容量受限的专家分派 × 序列位置。

专家缓冲容量用尽时，排在后面的 token 更易失去被分配的专家计算。

**怎么测**　按绝对及相对位置统计丢弃率，固定 token 分布、专家容量与分派顺序，比较训练和推理配置。

**证据范围**　OpenMoE 在 MT-Bench 上还观察到第二轮对话的表现下降得更多。作者把长序列末端较高的 token 丢弃率列为可能原因。被跳过的是该 token 的专家计算，它仍可沿残差路径传播。

[Xue 等（ICML 2024）](https://proceedings.mlr.press/v235/xue24c.html)

<a id="unargmaxable"></a>

## Unargmaxable Tokens · 输出层无法让某 token 成为 Top-1

**观察对象**　词表输出权重的几何。

某些模型存在一类特殊词元。任何隐藏向量输入其固定输出层，都无法将该词元作为严格的最高分预测。

**怎么测**　对每个候选词考察与全部竞争词的线性不等式是否可行。实际语料里从未贪心生成，只能作为筛选线索。

**证据范围**　Grivas 等检查了 150 个语言或翻译模型，其中 13 个有此类词元，数量很少。这个性质不排除采样生成。

[Grivas 等（ACL 2022）](https://aclanthology.org/2022.acl-long.465/)

<a id="expert-load-imbalance"></a>

## Expert Load Imbalance · 少数专家收到过多 token

**观察对象**　同一 MoE 层、同一批次中各专家获得的路由分配次数。

路由器可能反复选择少数专家，同时让其他专家空闲。在容量受限的模型里，过载专家会耗尽配额，部分 token 随之跳过专家计算。

**怎么测**　在容量截断之前按专家统计硬分配的 token 数，记录变异系数或路由熵。再报告各专家的溢出量和实际处理量，避免把容量上限误读成分配均匀。

**证据范围**　Fedus 等在使用 top-1 路由的 Switch Transformers 中分析了专家容量、溢出与负载均衡损失。位置维度可再单独统计，以判断溢出是否集中在序列末尾。

[Fedus 等（JMLR 2022）](https://jmlr.org/papers/v23/21-0998.html)

相关条目　[序列末尾的 token 被丢弃](routing-output.md#drop-towards-end)

<a id="context-independent-experts"></a>

## Context-Independent Expert Specialization · 专家选择主要由 token 身份决定

**观察对象**　固定层上，同一 token ID 在不同前缀中选中的专家。

某些已训练的解码器中，一个 token 即使用在不同语境，仍常被发给相同的少数专家。路由因此体现词表分工，模型对同一词在不同语境中的分派变化有限。

**怎么测**　固定 token ID 和层位，更换上下文并匹配 token 位置。统计首选专家的条件熵、top-k 集合的重叠，以及跨语境分布相对全层专家分布的差异。

**证据范围**　Xue 等在 OpenMoE 中称之为 Context-Independent Specialization。Arnold 等的模型比较发现，编码器路由受到语境的进一步调节，解码器的语境敏感度较弱。

[Xue 等（ICML 2024）](https://proceedings.mlr.press/v235/xue24c.html) · [Arnold 等（BlackboxNLP 2024）](https://aclanthology.org/2024.blackboxnlp-1.2/)

<a id="fragile-token-misrouting"></a>

## Expert Misrouting on Fragile Tokens · 困难 token 的默认专家组合错过低损失路由

**观察对象**　固定前缀和 MoE 层，同一 token 的默认与备选 top-k 专家集合。

在部分难预测的位置，冻结模型并换用同样数量的专家，就能提高已知下一词的概率。默认路由在这些位置很少选中实测最优的组合。

**怎么测**　在已验证的推理轨迹上，用相同前缀比较默认路由和采样的等计算量备选路由。按备选路由给真实下一词的平均概率分组，记录默认路由的排名，以及它与最佳备选路由的概率差。

**证据范围**　Yoon 等把备选路由平均概率不超过 0.5 的位置称为 fragile。在 Qwen3-30B-A3B 的最后一层 MoE、MATH Level-5 轨迹上，该组最佳采样路由比默认路由高 20.4 个百分点。比较使用已知下一词，是对既有轨迹的事后诊断。

[Yoon 等（arXiv 2026）](https://arxiv.org/abs/2605.07260)

<a id="undertrained-tokens"></a>

## Under-trained Tokens / Glitch Tokens · 词表中已收录但模型难以正常处理的 token

**观察对象**　单个词表 ID 的输入嵌入、输出权重与精确复述行为。

有些 token 可以由分词器合法产生，模型却难以按指令把它原样复述。分词器词表与实际训练文本覆盖之间的落差是论文讨论的一个来源。

**怎么测**　结合 tokenizer 的编码行为、嵌入或输出权重指标筛查候选，再用多种复述提示测试。核对输入 token ID、可见字符串和输出分词，并设置相近长度或频率的普通 token 作对照。

**证据范围**　Land 和 Bartolo 在多个 LLM 中验证了这类词元。权重与提示测得的是训练不足的迹象。一个词元复述失败，本身无法确定它在预训练中出现过几次，也无法证明它永远取不到 top-1。

[Land 与 Bartolo（EMNLP 2024）](https://aclanthology.org/2024.emnlp-main.649/)

相关条目　[线性输出层中的不可达 top-1 词元](routing-output.md#unargmaxable)

<a id="softmax-bottleneck"></a>

## Softmax Bottleneck · 线性输出头限制跨上下文的词表对数几率秩

**观察对象**　行是上下文、列是词表项的对数几率矩阵。

在线性词表投影加单一 softmax 的模型中，不同上下文可产生的词间对数几率受隐藏宽度约束。真实条件分布若需要更多独立方向，输出头就有表达瓶颈。

**怎么测**　固定参照词，用每个词相对它的 log 概率差组成矩阵，再沿上下文方向中心化。宽度为 d 的线性头即使含固定偏置，处理后的矩阵秩也至多为 d。可与高秩目标分布或混合 softmax 对照。

**证据范围**　Yang 等给出矩阵分解分析，并在 RNN 语言模型上比较了单一与混合 softmax。秩约束适用于同形式的 Transformer 输出头，是否限制某个具体任务仍取决于目标分布。

[Yang 等（ICLR 2018）](https://arxiv.org/abs/1711.03953)
