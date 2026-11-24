# LLM Phenomena Atlas

大语言模型内部现象的中文研究索引。收录 **55 项现象**、**66 篇原始论文或技术报告**。英文名称可直接用于检索。打开一项可以看到它发生在哪个张量、怎样测量、在哪些模型或理论条件下有证据。

**从三个现象开始**　[Rank Collapse](phenomena/representations.md#token-rank) · [Massive Activations](phenomena/activations.md#massive-activations) · [Attention Sinks](phenomena/attention.md#attention-sinks)

[完整论文索引](papers.md) · [可筛选的现象表（CSV）](phenomena.csv) · [11 项测量与干预方法](diagnostics.md)

## 现象目录

| 观察对象 | 条目数 | 现象 |
| --- | ---: | --- |
| [注意力与位置](phenomena/attention.md) | 10 | [Attention Sinks](phenomena/attention.md#attention-sinks) · [Secondary Attention Sinks](phenomena/attention.md#secondary-sinks) · [Attention Entropy Collapse](phenomena/attention.md#attention-entropy) · [Attention Collapse / Lazy Layers](phenomena/attention.md#attention-collapse) · [Active–Dormant Attention Heads](phenomena/attention.md#active-dormant) · [Attention Dispersion](phenomena/attention.md#dispersion) · [RoPE Relative-Position Breakdown in BF16](phenomena/attention.md#bf16-rope) · [Inter-head Linear Predictability](phenomena/attention.md#inter-head-predictability) · [Copy Suppression](phenomena/attention.md#copy-suppression) · [U-Shaped Positional Attention Bias / Lost in the Middle](phenomena/attention.md#lost-in-the-middle) |
| [激活、通道与神经元](phenomena/activations.md) | 14 | [Massive Activations](phenomena/activations.md#massive-activations) · [Value-State Drains](phenomena/activations.md#value-drains) · [Residual-State Peaks](phenomena/activations.md#residual-peaks) · [Outlier Features / Channels](phenomena/activations.md#outlier-channels) · [Residual Sinks / Outlier-Driven Rescaling](phenomena/activations.md#residual-sinks) · [Massive Values in Q and K](phenomena/activations.md#qk-outliers) · [Super Weights / Super Activations](phenomena/activations.md#super-weights) · [SwiGLU Weight Alignment / Outlier Amplification](phenomena/activations.md#swiglu) · [Lazy Neuron Phenomenon / Activation Sparsity](phenomena/activations.md#lazy-neurons) · [Weakening Neurons](phenomena/activations.md#weakening) · [Entropy Neurons](phenomena/activations.md#entropy-neurons) · [LayerNorm Outlier Dimensions](phenomena/activations.md#layernorm-outliers) · [Token Frequency Neurons](phenomena/activations.md#frequency-neurons) · [Dead Neurons](phenomena/activations.md#dead-neurons) |
| [表示几何与信息传播](phenomena/representations.md) | 9 | [Rank Collapse / Token Uniformity](phenomena/representations.md#token-rank) · [Rank Collapse in Width](phenomena/representations.md#rank-in-width) · [Compression Valleys](phenomena/representations.md#compression-valleys) · [Representation Anisotropy](phenomena/representations.md#anisotropy) · [Rogue Dimensions](phenomena/representations.md#rogue-dimensions) · [Information Over-Squashing](phenomena/representations.md#oversquashing) · [Subspace Collapse in Next-Token Prediction](phenomena/representations.md#subspace-collapse) · [Dimensional Collapse in Attention Outputs](phenomena/representations.md#attention-output-collapse) · [Token Embedding Degeneration](phenomena/representations.md#token-embedding-degeneration) |
| [深度、残差与训练动力学](phenomena/depth-training.md) | 13 | [Deep-Layer Ineffectiveness / Layer Redundancy](phenomena/depth-training.md#deep-ineffectiveness) · [Curse of Depth](phenomena/depth-training.md#curse-of-depth) · [Routing Collapse across Depth](phenomena/depth-training.md#depth-routing-collapse) · [Residual Stream Erasure](phenomena/depth-training.md#residual-erasure) · [Hydra Effect / Self-Repair](phenomena/depth-training.md#hydra-effect) · [Premature Upper-Layer Attention Specialization](phenomena/depth-training.md#premature-attention) · [Pre-LN Gradient Magnitude Mismatch](phenomena/depth-training.md#gradient-mismatch) · [Attention Logit Growth](phenomena/depth-training.md#logit-growth) · [Gradient Spikes](phenomena/depth-training.md#gradient-spikes) · [Gradient Sinks](phenomena/depth-training.md#gradient-sinks) · [Layer-wise Cyclic Fixed Points](phenomena/depth-training.md#cyclic-fixed-points) · [Latent Orbits](phenomena/depth-training.md#latent-orbits) · [Latent Overthinking](phenomena/depth-training.md#latent-overthinking) |
| [专家路由与词表输出](phenomena/routing-output.md) | 9 | [Representation Collapse in Sparse MoE](phenomena/routing-output.md#moe-collapse) · [Routing Fluctuation](phenomena/routing-output.md#routing-fluctuation) · [Drop-towards-the-End](phenomena/routing-output.md#drop-towards-end) · [Unargmaxable Tokens](phenomena/routing-output.md#unargmaxable) · [Expert Load Imbalance](phenomena/routing-output.md#expert-load-imbalance) · [Context-Independent Expert Specialization](phenomena/routing-output.md#context-independent-experts) · [Expert Misrouting on Fragile Tokens](phenomena/routing-output.md#fragile-token-misrouting) · [Under-trained Tokens / Glitch Tokens](phenomena/routing-output.md#undertrained-tokens) · [Softmax Bottleneck](phenomena/routing-output.md#softmax-bottleneck) |

## 容易混淆的三个问题

| 要问的问题 | 应该看什么 |
| --- | --- |
| 不同 token 的隐藏表示是否趋同？ | 层输出矩阵 $H$ 的谱与 token 间差异。[Token Rank Collapse](phenomena/representations.md#token-rank) |
| 不同 query 是否共享一套注意力分配？ | 单头权重矩阵 $A$ 的近似秩。如果每行仅看自己的位置，则 $A=I$，每行熵为零，矩阵仍然满秩。[Attention Collapse](phenomena/attention.md#attention-collapse) · [Attention Entropy Collapse](phenomena/attention.md#attention-entropy) |
| 是否有一个位置吸收许多 query 的注意力，而且确实改变了输出？ | 第一步看注意力矩阵该列收到的质量，第二步看加权 value 的写入或实做消融。[Attention Sinks](phenomena/attention.md#attention-sinks) · [Value-State Drains](phenomena/activations.md#value-drains) |

## 起点的七篇论文

| 论文 | 对应现象 |
| --- | --- |
| [The Unreasonable Ineffectiveness of the Deeper Layers](https://arxiv.org/abs/2403.17887) | [删除部分连续深层后仍能保留任务表现](phenomena/depth-training.md#deep-ineffectiveness) |
| [Stabilizing Transformer Training by Preventing Attention Entropy Collapse](https://proceedings.mlr.press/v202/zhai23a.html) | [注意力行熵坍缩](phenomena/attention.md#attention-entropy) |
| [Attention Is Not All You Need: Pure Attention Loses Rank Doubly Exponentially with Depth](https://proceedings.mlr.press/v139/dong21a.html) | [不同 token 的表示趋同](phenomena/representations.md#token-rank) |
| [The Curse of Depth in Large Language Models](https://arxiv.org/abs/2502.05795) | [深层残差更新相对输入变弱](phenomena/depth-training.md#curse-of-depth) |
| [Signal Propagation in Transformers: Theoretical Perspectives and the Role of Rank Collapse](https://arxiv.org/abs/2206.03126) | [不同 token 的表示趋同](phenomena/representations.md#token-rank) |
| [Mind the Gap: a Spectral Analysis of Rank Collapse and Signal Propagation in Attention Layers](https://proceedings.mlr.press/v267/nait-saada25a.html) | [上下文增长诱发的谱退化](phenomena/representations.md#rank-in-width) |
| [Massive Activations in Large Language Models](https://arxiv.org/abs/2402.17762) | [极少数标量激活异常大](phenomena/activations.md#massive-activations) |

收录范围包括会降低性能的失稳，也包括模型利用异常统计结构完成计算的情形。例如，裁剪某些离群通道会损害模型表现。每个条目分别写出行为观察和干预证据。[Residual Sinks](phenomena/activations.md#residual-sinks)
