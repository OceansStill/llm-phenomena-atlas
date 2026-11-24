# 测量与干预

这里收录 11 项诊断。每项说明应记录的量及其对照条件。文末给出正确答案保持率的两种定义，便于比较固定预算与超出训练轮数后的表现。

[现象索引](README.md) · [论文索引](papers.md)

## 通用 LLM

<a id="lens"></a>

### 逐层预测轨迹与答案翻转

在固定输入和同一预测位置，用最终的归一化与输出头读出每层状态（logit lens），或在独立数据上学习逐层仿射校准（tuned lens）。对齐正确词与竞争词的 logit 差、首次读对层、首次读对后又变错的层。Halawi 等比较真假 few-shot 示范，发现错误示范可使预测在某一层之后逐渐偏离。跨层读出能力与最终生成正确率应分别记录。

[Belrose 等（arXiv 2023）](https://arxiv.org/abs/2303.08112) · [Halawi 等（ICLR 2024）](https://arxiv.org/abs/2307.09476)

<a id="weighted-values"></a>

### 注意力加权值与实际写入

在每个头里计算单个来源对输出的向量贡献，即注意力权重乘 value 后再做该头的输出投影。并列查看接收权重、贡献范数以及贡献与总写入的夹角，能看到高权重小 value 或不同来源互相抵消。

[Kobayashi 等（EMNLP 2020）](https://aclanthology.org/2020.emnlp-main.574/) · [Guo 等（arXiv 2024）](https://arxiv.org/abs/2410.13835)

<a id="input-sensitivity"></a>

### 输入扰动与信息挤压

构造长度和任务相同的配对输入，只改决定答案的线索。比较最后位置的隐藏距离、两组正确答案的 logit margin 以及最终输出。分组改变线索距离与数值精度，可以定位哪类前缀差异在传播中消失。

[Barbero 等（NeurIPS 2024）](https://arxiv.org/abs/2406.04267)

<a id="geometry"></a>

### 余弦逐维分解与表示谱

对原始表示、去均值表示和逐维标准化表示分别统计随机对余弦及近邻排序。同时查看各维对点积的贡献和表示谱。训练/评估分割应在拟合去均值、标准化和学习读出前确定。

[Timkey 与 van Schijndel（EMNLP 2021）](https://arxiv.org/abs/2109.04404) · [Ethayarajh（EMNLP-IJCNLP 2019）](https://aclanthology.org/D19-1006/) · [Nait Saada 等（ICML 2025）](https://proceedings.mlr.press/v267/nait-saada25a.html)

<a id="activity"></a>

### 前馈单元活性分布

分别对每个样本计算活跃单元比例，对每个单元计算跨样本触发率。ReLU 可报告严格非零率，SwiGLU 应记录阈值和激活尺度。删除罕见单元时同时看稀有任务子集，以免平均分盖住专门功能。

[Li 等（ICLR 2023）](https://arxiv.org/abs/2210.06313) · [Gerstner 等（EMNLP 2026）](https://aclanthology.org/2026.emnlp-main.455/)

<a id="patching"></a>

### 激活替换与消融后的补偿

对语义匹配的干净输入和扰动输入，在同一层、位置替换激活，测量正确答案相对干扰答案的 logit 差恢复了多少。用同层随机位置或对照方向检验选择性。记录后层是否产生反向补偿，避免只用总效应判定模块无用。

[Zhang 与 Nanda（ICLR 2024）](https://arxiv.org/abs/2309.16042) · [McGrath 等（arXiv 2023）](https://arxiv.org/abs/2307.15771) · [Rushing 与 Nanda（ICML 2024）](https://proceedings.mlr.press/v235/rushing24a.html)

<a id="truth"></a>

### 正确性探针与外部行为

在独立训练集上，用答案相关 token 的隐藏状态拟合正确性探针，在留出集上报告 AUROC，并和模型输出置信度比较。按任务或数据集分割可检验可迁移性。能从状态预测错误只提供可读性证据，还可通过干预检验模型是否使用相应信息。

[Orgad 等（ICLR 2025）](https://arxiv.org/abs/2410.02707)

<a id="training"></a>

### 训练不稳定性监测

在同一批训练步对齐 logits 尺度、注意力行熵、裁剪前梯度、残差 RMS、损失和学习率。分别记录逐层统计、逐坐标尾部和时间序列，区分长时间失配与瞬时尖峰。控制优化器、精度或学习率后再比较。

[Zhai 等（ICML 2023）](https://proceedings.mlr.press/v202/zhai23a.html) · [Wortsman 等（ICLR 2024）](https://arxiv.org/abs/2309.14322) · [Huang 等（ICLR 2025）](https://proceedings.iclr.cc/paper_files/paper/2025/hash/7a70ad3d9c704fb9b81b5c69eda722dc-Abstract-Conference.html) · [Shleifer 等（arXiv 2021）](https://arxiv.org/abs/2110.09456)

## 跨层读取与循环模型

<a id="source-reads"></a>

### Attention Residual 的来源读取与消融

先写明来源是单个子层写入、块内写入和，还是累积隐藏状态。对每个来源分别记录读取权重、写入范数、加权写入在总和上的投影。固定其他权重后消去一项，与删除后重新归一化或重新运行路由分开报告。

[Kimi Team（2026 技术报告）](https://arxiv.org/abs/2603.15031) · [Luo 等（arXiv 2026）](https://arxiv.org/abs/2605.18855) · [Kobayashi 等（EMNLP 2020）](https://aclanthology.org/2020.emnlp-main.574/)

<a id="retain"></a>

### Loop 的计算、保持与复用

固定输入，对每一轮都读出答案，分别统计首次解出、指定后续轮仍然正确、连续保持正确。若有模拟器或可验证的中间步骤，可替换中间状态，观察后续是否能正确消费其内容。Zhou 等的实验对象是 Mealy 机的上下文执行任务。

[Zhou 等（arXiv 2026）](https://arxiv.org/abs/2610.19768)

<a id="directional"></a>

### 固定点附近的方向稳定性与潜在推理监测

同时比较相邻状态的相对更新量、答案 margin 与扰动后的多轮轨迹。在真实更新方向、随机方向、局部最大奇异方向分别加等范数微扰。TRM 的 Sudoku 实验观察到轨迹方向可能收缩，而其他方向仍扩张。对语言模型，另可对照固定前缀上不同循环次数和 prompt 扰动的读出。

[Krishna 等（arXiv 2026）](https://arxiv.org/abs/2609.26487) · [Olszowiec 等（arXiv 2026）](https://arxiv.org/abs/2610.18127)

## 可直接计算的量

以下是把现象用于新实验时可采用的操作定义。使用论文阈值复现特定结果时，以各篇原文为准。

- **矩阵谱。** 对指定矩阵 $X$ 的非零奇异值定义 $p_j=\sigma_j^2/\sum_k\sigma_k^2$。前一方向的能量占比为 $p_1$。可同时报告谱熵 $-\sum_j p_j\log p_j$、其指数，以及满足 $\sum_{j=1}^{r}p_j\geq\tau$ 的最小 $r$。明确写出矩阵是 $H$ 还是 $A$、是否中心化、阈值 $\tau$ 与对数底。零能量矩阵单独标记。
- **位置接收的注意力。** 对来源位置 $j$，在能看到它的 query 集合 $Q_j$ 上取平均，即 $s_j=\sum_{i\in Q_j}A_{ij}/|Q_j|$。长度、掩码和可见距离会改变基线，宜与同条件下的均匀注意力或匹配位置比较。
- **残差更新。** 对加法残差块记录 $\|h_{\ell+1}-h_\ell\|_2/(\|h_\ell\|_2+\varepsilon)$ 以及二者夹角。在替换式残差路由中，同时记录显式的来源混合与子层写入，才能区分读取变化和新计算。
- **条件正确保持率。** 令 $c_t(x)\in\{0,1\}$ 表示第 $t$ 轮的答案是否正确。以第 $t$ 轮答对的样本为队列，给出队列大小、末轮正确比例和全程正确比例。若队列为空，则记为未定义。

$$
R_{\mathrm{end}}(t,T)=
\frac{\sum_x c_t(x)c_T(x)}{\sum_x c_t(x)},\qquad
R_{\mathrm{all}}(t,T)=
\frac{\sum_x\prod_{u=t}^{T}c_u(x)}{\sum_x c_t(x)}.
$$

例如，一题在第 20、21、22 轮依次为「对、错、对」。从第 20 轮追踪至第 22 轮，它在末轮保持率中计为成功，在全程保持率中计为失败。若以「首次解出」构建队列，则只纳入首次答对恰好发生在第 $t$ 轮的样本。多轮自回归比较需固定提示词、输出位置和解码规则，或明确比较自由生成的整段答案。
