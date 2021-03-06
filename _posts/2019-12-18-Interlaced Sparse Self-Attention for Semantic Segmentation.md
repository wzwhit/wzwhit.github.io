---
layout:     post
title:      "Interlaced Sparse Self-Attention for Semantic Segmentation"
subtitle:   ""
date:       2019-12-18 11:30:00
author:     "wzw"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:     true
tags:
    - Non-local-opteration
    - Self-attention
    - DeepLearning
    - Semantic Segmentation
---
<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>
non-local block 计算消耗较大，这篇论文提出了 Interlaced Sparse Self-Attention，包括一个 long-range attention  和一个 short-range attention，用两个稀疏的相似矩阵代替了原来 self-attention 中密集的相似矩阵，减小了普通的 self-attention 的计算量。

[论文链接][paper-link]

## Method

#####   Interlaced Sparse Self-Attention

![1.png]({{ site.url }}/img/ISA/1.png)

ISA 处理一维输入的信息传递播示意图如上图所示。首先将输入特征图的位置（像素）均分为 Q 个子块，每个子块由 P 个位置组成。对于 long-range attention，我们从每一个子块中采集一个位置来构成 P 个包含 Q 个位置的子块，每个构造子集中的位置均具有较长的空间间隔距离。在每个构造子块上应用普通的 self-attention 来计算稀疏块相似矩阵
$$
A^L
$$
，根据
$$
A^L
$$
在每个子块间传播远距离信息。对于 short-range attention，直接在原始 Q 个子块上应用普通的 self-attention 来计算稀疏块相似矩阵
$$
A^S
$$
，根据
$$
A^S
$$
在临近位置之间传播信息。最后结合两个 attention 模块，就可以将信息从输入的每个位置传递到输出的每个位置。

## Model

![2.png]({{ site.url }}/img/ISA/2.png)

ISA 模型示意图如上图所示，其 python 代码如下：

![3.png]({{ site.url }}/img/ISA/3.png)

计算量：

![8.png]({{ site.url }}/img/ISA/8.png)

当
$$
P_hP_w=\sqrt{HW}
$$
时计算量最小。

## Experiment

![4.png]({{ site.url }}/img/ISA/4.png)

![4.png]({{ site.url }}/img/ISA/5.png)

![6.png]({{ site.url }}/img/ISA/6.png)

![7.png]({{ site.url }}/img/ISA/7.png)

[paper-link]: https://arxiv.org/abs/1907.12273

