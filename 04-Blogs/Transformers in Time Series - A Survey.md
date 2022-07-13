---
title: Transformers in Time Series - A Survey 
share: true
description:  
category: 笔记 
daily: false
createtime: 2022-07-13 07:40
tags:
- 笔记
---
```toc
```
## Summary

文章介绍了 Transformer 在时间序列数据数据方面的应用。从 Transformer 的网络架构变更、应用领域（预测、异常检测、分类）进行了分析。文章总结了一些在时间序列数据领域的代表性的 Transformer 方法，并通过实验，讨论了这些Transformer算法的优点和缺点，并且探讨了未来的研究方向。
 
## 原文解读
本章以原文段落为依据解析原文。

 ### 1  Introduction
由于 Transformer 在 NLP、CV（Computer vision，计算机视觉)、speech processing 的优异表现，Transformer成为了当下的研究热点。

Transformers 对于依赖范围大（longrange dependencies)、前后有关联（interactions)的序列数据（sequential data)有很强的建模能力，因此 Transformers 开始用于对时间序列(time series) 建模。[Li et al., 2019; Zhou et al., 2021; Zhou et al., 2022] 在时间序列预测方面做了研究，[Xu et al., 2022; Tuli et al., 2022] 在异常检测方面做了研究，[Zerveas et al., 2021; Yang et al., 2021] 在时间序列分类方面做了研究。

### 2 Preliminaries of the Transformer
本章介绍 Transformer 的一些基础知识。

**2.1 Vanilla Transformers** 
vanilla Transformer [Vaswani et al., 2017]  是一个神经序列模型，具有编码器-解码器的结构，其对数据没有做任何假设。

**2.2 Input Encoding and Positional Encoding** 
Transformer 使在输入中嵌入了位置编码（positional encoding），来对序列模型进行建模。Transformer 与 LSTM 和RNN不同，Transformer 没有递归（recurrence）和卷积（convolution）。
> It utilizes the positional encoding added in the input embeddings, to model the sequence information.

在 positional encoding 方面，主要分为了 *Absolute Positional Encoding* 和 *Relative Positional Encoding* 方法。

*Absolute Positional Encoding*
vanilla Transformer  使用了*Absolute Positional Encoding*的方法。 在  vanilla Transformer 中，对于每个位置索引 $t$,  位置编码向量由以下公式定义  $$P E(t)_{i}= \begin{cases}\sin \left(\omega_{i} t\right) & i \% 2=1 \\ \cos \left(\omega_{i} t\right) & i \% 2=0\end{cases}$$
$w_i$ 是每个维度手工指定的频率（类似于超参数）。
> where $ω_i$ is the hand-crafted frequency for each dimension. [Kenton and Toutanova, 2019; Gehring et al., 2017] 设计了自动学习 $w_i$ 的方法。


*Relative Positional Encoding*
[Shaw et al., 2018].提出了基于相对位置的位置嵌入。
[Ke et al., 2020]结合了相对位置嵌入和绝对位置嵌入。

**2.3 Multi-head Attention**
Attention 是 Transformer 中的核心部分。 在QKV（Query-Key-Value）的模型下，Transformers使用的缩放点乘attention 通过如下公式定义 $$\operatorname{Attention}(\mathbf{Q}, \mathbf{K}, \mathbf{V})=\operatorname{softmax}\left(\frac{\mathbf{Q K}^{\mathbf{T}}}{\sqrt{D_{k}}}\right) \mathbf{V}$$
**2.4 Feed-forward and Residual Network**
介绍了 Feed-forward and Residual Network 。


### 3 Taxonomy of Transformers in Time Series
为了总结学术界存在的针对时间序列的 Transormer，作者从网络修改（network modifications）和应用领域（application domains）两个方面对原文进行了分析，分类细节如下图所示
![](../aab/static/attachment/Pasted%20image%2020220713084230.png%5D)
在下文的两个章节中，作者会从这两个方面深入分析。

### 4 Network Modifications for Time Series
本章介绍 Transorfor 模型的网络架构在时间序列上的迭代更新。

*4.1 Positional Encoding*
因为Transformers 和排列组合相同且时间序列的位置也很重要，所以很有必要将位置信息嵌入到 Transformer 中。一种简单的设计是：
- 将位置信息处理为向量
- 将这个向量作为额外的输入，与时间序列数据一同输入
上面这种设计有三种实现方法，分别是：Vanilla Positional Encoding 、Learnable Positional Encoding 、Timestamp Encoding.。

*Vanilla Positional Encoding*
手工指定，表现力和适应能力差，无法充分利用时间序列数据的重要特征、
> hand-crafted and less expressive and adaptive. unable to fully exploit the important features of time series data.

*Learnable Positional Encoding*
下面两个人的工作解决了*Vanilla Positional Encoding* 中存在的问题，通过自动学习的方式学习位置特征。
>[Zerveas et al., 2021] introduce an embedding layer in Transformer that learn embedding vectors for each position index jointly with other model parameters. [Lim et al., 2019] used an LSTM network to encode positional embeddings, with the aim to better exploit sequential ordering information in time series.

*Timestamp Encoding*
时间戳也是很重要的一部分信息（如节假日），这些时间信息无法再 vanilla Transformers 中进行应用。为了解决这个问题，Informer [Zhou et al., 2021]、Autoformer [Wu et al., 2021]、FEDformer [Zhou et al., 2022] 提出了集成时间戳的模型。

> To mitigate the issue, Informer [Zhou et al., 2021] proposed to encode timestamps as additional positional encoding by using learnable embedding layers. A similar timestamp ecoding scheme was used in Autoformer [Wu et al., 2021] and FEDformer [Zhou et al., 2022]

*4.2 Attention Module*
Transformer 的核心是 self-attention 模块，self-attention 可以被看做是带有权重的全连接层，根据输入模式的成对相似性动态生成（It can be viewed as a fully connected layer with the weights that are dynamically generated based on the pairwise similarity of input patterns.）。

接下来分析各个模型的时间和内存复杂度。vanilla Transformer 的时间和内存复杂度是 $O(l^2)$,  使vanilla Transformer 在处理长时间序列的时候存在计算瓶颈。LogTrans [Li et al., 2019] and Pyraformer [Liu et al., 2022] 通过将稀疏性偏差显式引入 attention 减少计算量。Informer [Zhou et al., 2021] and FEDformer [Zhou et al., 2022]通过引入一种低水平的熟悉减少计算量。各模型的时间复杂度和内存复杂度图所示
![](../aab/static/attachment/Pasted%20image%2020220713091535.png%5D)

**4.3 Architecture-Level Innovation**
介绍各个 Transformer 模型在网络结构上的变化以及更新，以及其优缺点。

### 5 Applications of Time Series Transformers
本章介绍 Time Series Transformers  在预测（Forecasting）、异常检测（ Anomaly Detection）、分类（Classification）的相关工作及其优缺点。

**5.1 Transformers in Forecasting**
*Time Series Forecasting*
介绍了 LogTrans [Li et al., 2019] 、Informer [Zhou et al., 2021]、AST [Wu et al., 2020]、Autoformer [Wu et al., 2021]、FEDformer [Zhou et al., 2022]、TFT [Lim et al., 2021]、SSDNet [Lin et al., 2021]、Pyraformer [Liu et al., 2022]、Aliformer [Qi et al., 2021]的改进以及优点。

*Spatio-Temporal Forecasting*
介绍了Traffic Transformer [Cai et al., 2020]、Spatialtemporal Transformer [Xu et al., 2020]、Spatio-temporal graph Transformer [Yu et al., 2020] 的改进以及优点。 

*Event Forecasting*
介绍了 (TPP) [Shchur et al., 2021]、Self-attentive Hawkes process (SAHP) [Zhang et al., 2020] 、 Transformer Hawkes process (THP) [Zuo et al., 2020]、Datalog through time (A-NDTT) [Mei et al., 2022]的改进以及优点。 


**5.2 Transformers in Anomaly Detection**
介绍了 TranAD [Tuli et al., 2022],MT-RVAE [Wang et al., 2022], and TransAnomaly [Zhang et al., 2021]、GTA [Chen et al., 2021d] 的改进以及优点。 

**5.3 Transformers in Classification**
GTN [Liu et al., 2021]、[Rußwurm and K ̈ orner, 2020]、[Yuan and Lin, 2020]、[Zerveas et al., 2021]、[Yang et al., 2021]在时间序列分类方面的研究介绍。

### 6 Experimental Evaluation and Discussion
本章通过数据集（ benchnmark dataset ETTm2 [Zhou et al., 2021]）上对上述提到的 Transformer 进行了三方面的验证：
1. 在长时间序列（long-term sequence）的性能（通过 MSE 评估）
2. 网络层数（layers）对模型性能的影响
3. 在Seasonal-Trend Decomposition Analysis的性能

Robustness Analysis
上面介绍的这些Transormer模型中，为了减少计算和内存复杂度，他们都是用了固定大小的输入来获得更好的实验效果。但是，在实际应用中，我们无法知道其设计是否真正的有高效。文章设计了实验来验证这些算法在长时间序列（long-term sequence)的稳健性（rubust),。

实验结果如下所示
![](../aab/static/attachment/Pasted%20image%2020220713093128.png%5D)
结论是：这些精心设计的Transformer无法在 long-term sequence 中获得更好的效果，更多的工作应该关注 long sequence 。

*Model Size Analysis*
实验探索了 layers 的层数与模型性能的对比，结果如下所示
![](../aab/static/attachment/Pasted%20image%2020220713094330.png%5D)
从表中可知，layer 为3-6层的神经网络的性能优于更高层layers的神经网络，结论：如何设计一个正确的 Transformer，使其随着模型复杂度的增加实现更好的性能。


*Seasonal-Trend Decomposition Analysis*
分析各个 Transformer 在 seasonal-trend decomposition  上的性能。

### 7 Future Research Opportunities
本章探讨了未来可能的研究方向。

7.1 结合时间序列数据的更多特征，开发针对特定任务的 Transformer 
7.2 结合 Transformer 和 GNN（图神经网络）处理多维时间序列数据
7.3 如何开发针对时间序列数据的 pre-trained Transformer model 
7.4 使用自动机器学习自动发现计算和内存架构的 Transformer

### 8 Conclusion
讨论了 Transformers 在时间序列数据中的应用，分别从模型修改和应用领域两个维度对 Transormer 进行分析。在每个类别中，总结了一些具有代表性的方法，通过实验，讨论了他们的优点和缺点，并且明确了未来的研究方向。