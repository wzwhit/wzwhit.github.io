---
layout:     post
title:      "Multi-lane Detection Using Instance Segmentation and Attentive Voting "
subtitle:   ""
date:       2020-02-25 17:20:00
author:     "wzw"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:     true
tags:
    - DeepLearning
    - Semantic Segmentation
    - Lane Detection
---
<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>
ICCAS 2019的一篇车道线检测论文。文章使用了自己的车道线数据集（5000张），分割网络很简单，但是后处理创新地提出了 Attentive Voting 方法来判断两条实例的线是否属于同一条车道线。

[论文链接][paper-link]

## Model

![1.png]({{ site.url }}/img/0225/1.png)

整个车道线检测过程示意图如上图所示：首先是语义分割，然后聚类出实例，然后进行透视变换为 Bird’s Eye View，然后是无监督的 Attentive Voting ，然后曲线拟合，最后逆透视变换回来输出。

1. 语义分割网络模型如下表所示，就是简单的卷积加反卷积的 U 型结构。损失函数使用的是  soft dice loss。

   ![2.png]({{ site.url }}/img/0225/2.png)

2. 实例检测使用的是广度优先搜索算法。这之后进行透视变换是为了减小之后拟合曲线的多项式次数。

3. 无监督的 Attentive Voting 示意图如下所示：

![3.png]({{ site.url }}/img/0225/3.png)

图中 Li 代表车道线掩码实例，
$$
\vec{Li_{min}}
$$
和
$$
\vec{Li_{max}}
$$
代表车道线掩码 Li 最下方和最上方的像素点；车道线掩码 Li 和 Lj 之间的的 Attentive vote 表示为
$$
vote(L1,L2) = d1 + d2
$$
，其中 di 表示点 P 到由车道线掩码 Li 拟合出的曲线的法线方程的垂直距离；点 P 论文中没说，从图中可以看出
$$
\vec{P} = (\vec{Li_{min}}+\vec{Li_{max}}) / 2
$$
。如果计算出的 vote 小于阈值
$$
\eta
$$
，则认为这两个实例属于同一条车道线。这个无监督的 Attentive Voting 方法解决了聚类时将同一车道线的不同段错分为两条线的问题。

## Experiment

速度：

![4.png]({{ site.url }}/img/0225/4.png)

可视化：

![5.png]({{ site.url }}/img/0225/5.png)



[paper-link]: https://arxiv.org/abs/2001.00236
