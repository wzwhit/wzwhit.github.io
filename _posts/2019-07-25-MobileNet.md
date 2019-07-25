---
layout:     post
title:      "MobileNet"
subtitle:   ""
date:       2019-07-25 15:00:00
author:     "wzw"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:     true
tags:
    - DeepLearning
---
<script type="text/javascript" async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"> </script>

[MobileNetV1][MobileNetV1] & [MobileNetV2 ][MobileNetV2] 轻量模型

## MobileNetV1

主要思想就是提出了深度可分离卷积 Depthwise Separable Convolution，结构如下图所示：

![1.png]({{ site.url }}/img/MobileNet/mobilenet-1.png)

深度可分离卷积先分别在输入特征图的每一个通道上应用一个卷积，称为 depthwise convolution，再用一个1*1的卷积去融合 depthwise conv 输出的不同通道的特征之间的关系，称为 pointwise convolution。而标准卷积直接就输出了这两步的结果。

这样做的好处就是计算量大大减少：

输入特征图 F 维度为：
$$
D_F*D_F*M
$$
，输出 G 维度：
$$
D_G*D_G*N
$$
，标准卷积核尺寸为：
$$
D_K*D_K*M*N
$$
，所以标准卷积计算量为：
$$
D_K*D_K*M*N*D_F*D_F
$$


而对于深度可分离卷积，depthwise conv 的卷积核为：
$$
D_K*D_K*M
$$
，pointwise conv 的卷积核为：
$$
1*1* M *N
$$
，深度可分离卷积两步总的计算量为：
$$
D_K*D_K*M *D_F *D_F+M*N*D_F*D_F
$$


相比于标准卷积，参数量比率为：
$$
\frac{D_K*D_K*M *D_F *D_F+M*N*D_F*D_F}{D_K*D_K*M*N*D_F*D_F}=\frac{1}{N}+\frac{1}{D_K^2}
$$
深度可分离卷积结构如下图右边所示，左边为标准卷积：

![2.png]({{ site.url }}/img/MobileNet/mobilenet-2.png)

MobileNet 结构如下表所示：

![3.png]({{ site.url }}/img/MobileNet/mobilenet-3.png)

## MobileNetV2

MobileNetV2 论文写的很深奥，大致思想两点：

1. Linear Bottlenecks
2. Inverted residuals

##### Linear Bottlenecks

为了减少网络参数量，可以减少每层输出的通道数，但是 ReLU 激活函数应用在少的通道数上会大大破坏信息完整度，作者做了如下图实验：

![4.png]({{ site.url }}/img/MobileNet/mobilenet-4.png)

作者将一个螺旋特征(图左一所示)通过一个 n×m 维(螺旋是二维图像则 m=2)的矩阵 T 变换到 n 维的空间中，经过 ReLU 之后，再利用
$$
T^{-1}
$$
恢复到二维空间，可以看到，当 n=2，3 时信息丢失较多，n=15，30 的时候信息恢复的比较好。

因此，作者认为在输出通道数较少的时候不能用 ReLU，因此把深度可分离卷积最后的 ReLU换成了线性操作，也就是 1*1 的通道卷积。具体结构如下图所示：

![5.png]({{ site.url }}/img/MobileNet/mobilenet-5.png)

图中 a 是标准卷积，b 是深度可分离卷积，c 是改为线性操作的可分离卷积，d 是 c 堆叠起来的结构，也就是线性操作之后的输出作为下一个分离卷积的输入。

##### Inverted residuals

Inverted 是 ‘倒’ 的意思，啥是倒的残差结构呢，看下图：

![6.png]({{ site.url }}/img/MobileNet/mobilenet-6.png)

左边是普通的残差结构，右边是 Inverted residuals。可以看到，倒的是通道数量：普通Residual block 输入输出通道数多，block 内部通道数少；而 Inverted residuals block 相反，输入输出通道数少，block 内部通道数多。Inverted residuals block 结构如下表和图所示：

![8.png]({{ site.url }}/img/MobileNet/mobilenet-8.png)

![7.png]({{ site.url }}/img/MobileNet/mobilenet-7.png)

MobileNetV2 模型结构如下：

![9.png]({{ site.url }}/img/MobileNet/mobilenet-9.png)



[MobileNetV1]: https://arxiv.org/abs/1704.04861
[MobileNetV2]: https://arxiv.org/abs/1801.04381