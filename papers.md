# 原始论文索引

共 49 篇。前七篇对应最初的阅读清单，其余按题名字母排序。每篇都链接到原文及本仓库相关条目。标注 arXiv 的条目使用预印本记录及首次发布年份。

[返回现象目录](README.md) · [测量与干预](diagnostics.md)

## 起点的七篇论文

<a id="paper-deep"></a>

- **[The Unreasonable Ineffectiveness of the Deeper Layers](https://arxiv.org/abs/2403.17887)**　Gromov 等，ICLR 2025。

  对应条目　[Deep-Layer Ineffectiveness / Layer Redundancy](phenomena/depth-training.md#deep-ineffectiveness)

<a id="paper-entropy"></a>

- **[Stabilizing Transformer Training by Preventing Attention Entropy Collapse](https://proceedings.mlr.press/v202/zhai23a.html)**　Zhai 等，ICML 2023。

  对应条目　[Attention Entropy Collapse](phenomena/attention.md#attention-entropy) · [Attention Logit Growth](phenomena/depth-training.md#logit-growth) · [训练不稳定性监测](diagnostics.md#training)

<a id="paper-tokenrank"></a>

- **[Attention Is Not All You Need: Pure Attention Loses Rank Doubly Exponentially with Depth](https://proceedings.mlr.press/v139/dong21a.html)**　Dong 等，ICML 2021。

  对应条目　[Rank Collapse / Token Uniformity](phenomena/representations.md#token-rank)

<a id="paper-curse"></a>

- **[The Curse of Depth in Large Language Models](https://arxiv.org/abs/2502.05795)**　Wenfang Sun 等，NeurIPS 2025。

  对应条目　[Curse of Depth](phenomena/depth-training.md#curse-of-depth)

<a id="paper-noci"></a>

- **[Signal Propagation in Transformers: Theoretical Perspectives and the Role of Rank Collapse](https://arxiv.org/abs/2206.03126)**　Noci 等，NeurIPS 2022。

  对应条目　[Rank Collapse / Token Uniformity](phenomena/representations.md#token-rank)

<a id="paper-gap"></a>

- **[Mind the Gap: a Spectral Analysis of Rank Collapse and Signal Propagation in Attention Layers](https://proceedings.mlr.press/v267/nait-saada25a.html)**　Nait Saada 等，ICML 2025。

  对应条目　[Rank Collapse in Width](phenomena/representations.md#rank-in-width) · [余弦逐维分解与表示谱](diagnostics.md#geometry)

<a id="paper-mass"></a>

- **[Massive Activations in Large Language Models](https://arxiv.org/abs/2402.17762)**　Mingjie Sun 等，COLM 2024。

  对应条目　[Massive Activations](phenomena/activations.md#massive-activations) · [Residual-State Peaks](phenomena/activations.md#residual-peaks)

## 其他现象与诊断文献

<a id="paper-resink"></a>

- **[A Unified View of Attention and Residual Sinks: Outlier-Driven Rescaling is Essential for Large Language Model Training](https://proceedings.colm.cc/paper_files/paper/2026/hash/19f10adb6749b0c9f1ff7610bd01d44d-Abstract-Conference.html)**　Qiu 等，COLM 2026。

  对应条目　[Residual Sinks / Outlier-Driven Rescaling](phenomena/activations.md#residual-sinks)

<a id="paper-drain"></a>

- **[Active-Dormant Attention Heads: Mechanistically Demystifying Extreme-Token Phenomena in LLMs](https://arxiv.org/abs/2410.13835)**　Guo 等，arXiv 2024。

  对应条目　[Attention Sinks](phenomena/attention.md#attention-sinks) · [Active–Dormant Attention Heads](phenomena/attention.md#active-dormant) · [Value-State Drains](phenomena/activations.md#value-drains) · [Residual-State Peaks](phenomena/activations.md#residual-peaks) · [注意力加权值与实际写入](diagnostics.md#weighted-values)

<a id="paper-rogue"></a>

- **[All Bark and No Bite: Rogue Dimensions in Transformer Language Models Obscure Representational Quality](https://arxiv.org/abs/2109.04404)**　Timkey 与 van Schijndel，EMNLP 2021。

  对应条目　[Rogue Dimensions](phenomena/representations.md#rogue-dimensions) · [余弦逐维分解与表示谱](diagnostics.md#geometry)

<a id="paper-erase"></a>

- **[An Adversarial Example for Direct Logit Attribution: Memory Management in GELU-4L](https://aclanthology.org/2024.blackboxnlp-1.15/)**　Janiak 等，BlackboxNLP 2024。

  对应条目　[Residual Stream Erasure](phenomena/depth-training.md#residual-erasure)

<a id="paper-kobayashi"></a>

- **[Attention is Not Only a Weight: Analyzing Transformers with Vector Norms](https://aclanthology.org/2020.emnlp-main.574/)**　Kobayashi 等，EMNLP 2020。

  对应条目　[Value-State Drains](phenomena/activations.md#value-drains) · [注意力加权值与实际写入](diagnostics.md#weighted-values) · [Attention Residual 的来源读取与消融](diagnostics.md#source-reads)

<a id="paper-attnres"></a>

- **[Attention Residuals](https://arxiv.org/abs/2603.15031)**　Kimi Team，2026 技术报告。

  对应条目　[Routing Collapse across Depth](phenomena/depth-training.md#depth-routing-collapse) · [Attention Residual 的来源读取与消融](diagnostics.md#source-reads)

<a id="paper-valleys"></a>

- **[Attention Sinks and Compression Valleys in LLMs are Two Sides of the Same Coin](https://arxiv.org/abs/2510.06477)**　Queipo-de-Llano 等，ICLR 2026。

  对应条目　[Compression Valleys](phenomena/representations.md#compression-valleys)

<a id="paper-grad-sink"></a>

- **[Attention Sinks Induce Gradient Sinks: Massive Activations as Gradient Regulators in Transformers](https://arxiv.org/abs/2603.17771)**　Chen 等，arXiv 2026。

  对应条目　[Gradient Sinks](phenomena/depth-training.md#gradient-sinks)

<a id="paper-entropyneur"></a>

- **[Confidence Regulation Neurons in Language Models](https://proceedings.neurips.cc/paper_files/paper/2024/hash/e21955c93dede886af1d0d362c756757-Abstract-Conference.html)**　Stolfo 等，NeurIPS 2024。

  对应条目　[Entropy Neurons](phenomena/activations.md#entropy-neurons)

<a id="paper-layerroute"></a>

- **[Delta Attention Residuals](https://arxiv.org/abs/2605.18855)**　Luo 等，arXiv 2026。

  对应条目　[Routing Collapse across Depth](phenomena/depth-training.md#depth-routing-collapse) · [Attention Residual 的来源读取与消融](diagnostics.md#source-reads)

<a id="paper-sinks"></a>

- **[Efficient Streaming Language Models with Attention Sinks](https://arxiv.org/abs/2309.17453)**　Xiao 等，ICLR 2024。

  对应条目　[Attention Sinks](phenomena/attention.md#attention-sinks)

<a id="paper-lens"></a>

- **[Eliciting Latent Predictions from Transformers with the Tuned Lens](https://arxiv.org/abs/2303.08112)**　Belrose 等，arXiv 2023。

  对应条目　[逐层预测轨迹与答案翻转](diagnostics.md#lens)

<a id="paper-repair"></a>

- **[Explorations of Self-Repair in Language Models](https://proceedings.mlr.press/v235/rushing24a.html)**　Rushing 与 Nanda，ICML 2024。

  对应条目　[Hydra Effect / Self-Repair](phenomena/depth-training.md#hydra-effect) · [激活替换与消融后的补偿](diagnostics.md#patching)

<a id="paper-anis"></a>

- **[How Contextual are Contextualized Word Representations? Comparing the Geometry of BERT, ELMo, and GPT-2 Embeddings](https://aclanthology.org/D19-1006/)**　Ethayarajh，EMNLP-IJCNLP 2019。

  对应条目　[Representation Anisotropy](phenomena/representations.md#anisotropy) · [余弦逐维分解与表示谱](diagnostics.md#geometry)

<a id="paper-subspace"></a>

- **[Implicit Geometry of Next-token Prediction: From Language Sparsity Patterns to Model Representations](https://arxiv.org/abs/2408.15417)**　Zhao 等，2024。

  对应条目　[Subspace Collapse in Next-Token Prediction](phenomena/representations.md#subspace-collapse)

<a id="paper-premature"></a>

- **[Learning Less Is More: Premature Upper-Layer Attention Specialization Hurts Language Model Pretraining](https://arxiv.org/abs/2605.10504)**　Zhu 等，arXiv 2026。

  对应条目　[Premature Upper-Layer Attention Specialization](phenomena/depth-training.md#premature-attention)

<a id="paper-retain"></a>

- **[Learning to Compute Is Not Learning to Retain or Reuse in Looped Transformers](https://arxiv.org/abs/2610.19768)**　Zhou 等，arXiv 2026。

  对应条目　[Loop 的计算、保持与复用](diagnostics.md#retain)

<a id="paper-outliers"></a>

- **[LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale](https://arxiv.org/abs/2208.07339)**　Dettmers 等，NeurIPS 2022。

  对应条目　[Outlier Features / Channels](phenomena/activations.md#outlier-channels)

<a id="paper-truth"></a>

- **[LLMs Know More Than They Show: On the Intrinsic Representation of LLM Hallucinations](https://arxiv.org/abs/2410.02707)**　Orgad 等，ICLR 2025。

  对应条目　[正确性探针与外部行为](diagnostics.md#truth)

<a id="paper-unarg"></a>

- **[Low-Rank Softmax Can Have Unargmaxable Classes in Theory but Rarely in Practice](https://aclanthology.org/2022.acl-long.465/)**　Grivas 等，ACL 2022。

  对应条目　[Unargmaxable Tokens](phenomena/routing-output.md#unargmaxable)

<a id="paper-qk"></a>

- **[Massive Values in Self-Attention Modules are the Key to Contextual Knowledge Understanding](https://proceedings.mlr.press/v267/jin25f.html)**　Jin 等，ICML 2025。

  对应条目　[Massive Values in Q and K](phenomena/activations.md#qk-outliers)

<a id="paper-monitor"></a>

- **[Monitoring latent reasoning in looped language models](https://arxiv.org/abs/2610.18127)**　Olszowiec 等，arXiv 2026。

  对应条目　[固定点附近的方向稳定性与潜在推理监测](diagnostics.md#directional)

<a id="paper-normformer"></a>

- **[NormFormer: Improved Transformer Pretraining with Extra Normalization](https://arxiv.org/abs/2110.09456)**　Shleifer 等，arXiv 2021。

  对应条目　[Pre-LN Gradient Magnitude Mismatch](phenomena/depth-training.md#gradient-mismatch) · [训练不稳定性监测](diagnostics.md#training)

<a id="paper-secondary"></a>

- **[On the Existence and Behavior of Secondary Attention Sinks](https://arxiv.org/abs/2512.22213)**　Wong 等，arXiv 2025。

  对应条目　[Secondary Attention Sinks](phenomena/attention.md#secondary-sinks)

<a id="paper-moerep"></a>

- **[On the Representation Collapse of Sparse Mixture of Experts](https://proceedings.neurips.cc/paper_files/paper/2022/hash/df4f371f1f89ec8ba5014b3310578048-Abstract.html)**　Chi 等，NeurIPS 2022。

  对应条目　[Representation Collapse in Sparse MoE](phenomena/routing-output.md#moe-collapse)

<a id="paper-drop"></a>

- **[OpenMoE: An Early Effort on Open Mixture-of-Experts Language Models](https://proceedings.mlr.press/v235/xue24c.html)**　Xue 等，ICML 2024。

  对应条目　[Drop-towards-the-End](phenomena/routing-output.md#drop-towards-end)

<a id="paper-halawi"></a>

- **[Overthinking the Truth: Understanding how Language Models Process False Demonstrations](https://arxiv.org/abs/2307.09476)**　Halawi 等，ICLR 2024。

  对应条目　[逐层预测轨迹与答案翻转](diagnostics.md#lens)

<a id="paper-swiglu"></a>

- **[Scaling FP8 training to trillion-token LLMs](https://proceedings.iclr.cc/paper_files/paper/2025/hash/f48b5133e89854a9e97cc22a6db83f25-Abstract-Conference.html)**　Fishman 等，ICLR 2025。

  对应条目　[SwiGLU Weight Alignment / Outlier Amplification](phenomena/activations.md#swiglu)

<a id="paper-spread"></a>

- **[Small-scale proxies for large-scale Transformer training instabilities](https://arxiv.org/abs/2309.14322)**　Wortsman 等，ICLR 2024。

  对应条目　[Attention Logit Growth](phenomena/depth-training.md#logit-growth) · [Gradient Spikes](phenomena/depth-training.md#gradient-spikes) · [训练不稳定性监测](diagnostics.md#training)

<a id="paper-disp"></a>

- **[Softmax is not Enough (for Sharp Size Generalisation)](https://proceedings.mlr.press/v267/velickovic25a.html)**　Veličković 等，ICML 2025。

  对应条目　[Attention Dispersion](phenomena/attention.md#dispersion)

<a id="paper-spikes"></a>

- **[SPAM: Spike-Aware Adam with Momentum Reset for Stable LLM Training](https://proceedings.iclr.cc/paper_files/paper/2025/hash/7a70ad3d9c704fb9b81b5c69eda722dc-Abstract-Conference.html)**　Huang 等，ICLR 2025。

  对应条目　[Gradient Spikes](phenomena/depth-training.md#gradient-spikes) · [训练不稳定性监测](diagnostics.md#training)

<a id="paper-moeroute"></a>

- **[StableMoE: Stable Routing Strategy for Mixture of Experts](https://aclanthology.org/2022.acl-long.489/)**　Dai 等，ACL 2022。

  对应条目　[Routing Fluctuation](phenomena/routing-output.md#routing-fluctuation)

<a id="paper-hydra"></a>

- **[The Hydra Effect: Emergent Self-repair in Language Model Computations](https://arxiv.org/abs/2307.15771)**　McGrath 等，arXiv 2023。

  对应条目　[Hydra Effect / Self-Repair](phenomena/depth-training.md#hydra-effect) · [激活替换与消融后的补偿](diagnostics.md#patching)

<a id="paper-lazy"></a>

- **[The Lazy Neuron Phenomenon: On Emergence of Activation Sparsity in Transformers](https://arxiv.org/abs/2210.06313)**　Li 等，ICLR 2023。

  对应条目　[Lazy Neuron Phenomenon / Activation Sparsity](phenomena/activations.md#lazy-neurons) · [前馈单元活性分布](diagnostics.md#activity)

<a id="paper-super"></a>

- **[The Super Weight in Large Language Models](https://arxiv.org/abs/2411.07191)**　Yu 等，arXiv 2024。

  对应条目　[Super Weights / Super Activations](phenomena/activations.md#super-weights)

<a id="paper-patch"></a>

- **[Towards Best Practices of Activation Patching in Language Models: Metrics and Methods](https://arxiv.org/abs/2309.16042)**　Zhang 与 Nanda，ICLR 2024。

  对应条目　[激活替换与消融后的补偿](diagnostics.md#patching)

<a id="paper-over"></a>

- **[Transformers need glasses! Information over-squashing in language tasks](https://arxiv.org/abs/2406.04267)**　Barbero 等，NeurIPS 2024。

  对应条目　[Information Over-Squashing](phenomena/representations.md#oversquashing) · [输入扰动与信息挤压](diagnostics.md#input-sensitivity)

<a id="paper-weaken"></a>

- **[Weakening Neurons: An Input-Output Functionality in Transformers with Outsize Influence](https://aclanthology.org/2026.emnlp-main.455/)**　Gerstner 等，EMNLP 2026。

  对应条目　[Weakening Neurons](phenomena/activations.md#weakening) · [前馈单元活性分布](diagnostics.md#activity)

<a id="paper-attentioncollapse"></a>

- **[When Attention Collapses: How Degenerate Layers in LLMs Enable Smaller, Stronger Models](https://openreview.net/pdf?id=2zQn0bUoPf)**　Sanyal 等，TMLR 2026。

  对应条目　[Attention Collapse / Lazy Layers](phenomena/attention.md#attention-collapse)

<a id="paper-emerge"></a>

- **[When Attention Sink Emerges in Language Models: An Empirical View](https://arxiv.org/abs/2410.10781)**　Gu 等，ICLR 2025。

  对应条目　[Attention Sinks](phenomena/attention.md#attention-sinks)

<a id="paper-bfrope"></a>

- **[When Precision Meets Position: BFloat16 Breaks Down RoPE in Long-Context Training](https://arxiv.org/abs/2411.13476)**　Wang 等，TMLR 2025。

  对应条目　[RoPE Relative-Position Breakdown in BF16](phenomena/attention.md#bf16-rope)

<a id="paper-fixed"></a>

- **[When Recursive Models Finish Computing](https://arxiv.org/abs/2609.26487)**　Krishna 等，arXiv 2026。

  对应条目　[固定点附近的方向稳定性与潜在推理监测](diagnostics.md#directional)
