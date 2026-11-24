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
