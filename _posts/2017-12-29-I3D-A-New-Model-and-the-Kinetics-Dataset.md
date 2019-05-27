---
layout:     post
title:     I3D A New Model and the Kinetics Dataset
subtitle:   人体动作识别论文阅读-姿态检测
date:       2017-12-29
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition
---

## 摘要

- 一是发布了新的trimmed video dataset——***Kinetics***

Kinetics数据集来源于油管，包含400类行为，每一类有大于400条的视频并且一条视频都是截取于独立的视频。每一段视频大概10s左右，测试集每一类大概有100段。包含4种主要分类：

1. 个人行为

2. 人际的交互行为

3. 需要时间推理的行为

4. 强调对对象进行区分的行为。

- 二是在这个数据集上训练了一个新网络——***Inflating 3D ConvNets***

以往的Conv3D效果很差的原因之一就是数据集太小，喂不饱网络。文章中的3D网络并不是随机初始化的，而是将在ImageNet训好的2D模型参数展开成3D，之后再训练。因此叫Inflating 3D ConvNets。

## 模型分析

文章梳理了各时期用深度学习来做行为识别的典型方法：

![img](https://note.youdao.com/yws/public/resource/4810e926dd8d78414bfc6a3aa45c7b0c/xmlnote/7470C48414534333983FC58A30615D4A/6436)

**a) LSTM（LRCN 14.11）**

![img](https://note.youdao.com/yws/public/resource/4810e926dd8d78414bfc6a3aa45c7b0c/xmlnote/5D88A44374984BFA8ABFA2A8D414043A/6476)

**b) 3D-ConvNet（14.10）**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

 vedio clip: c\*l\*h\*w (3\*16\*128\*171)

3D kernel : d\*k\*k    (3\*3\*3)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

用实验证明，3\*3\*3的时空卷积核，是最适合于这种新型神经网络结构的卷积核。

**c) Two-Stream（14.6）**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

空间和时间网络分别给出动作的分类结果，然后把结果融合，使用取平均值或者SVM的方法（实验中显示SVM准确率更高），得到最终结果。

**d) 3D-Fused Two-Stream（16.4）**

以前的 two-stream architecture 缺点：

- 只在最后一层分类结果融合，不能很好的学习到每个像素点的时空关联
- 时间尺度的限制：只输入一张RGB图片和L（10）张flow图片

探讨怎么在 CNN中更好的融合时空信息。 发现有以下三点： 

(i) 相比于softmax层融合，卷积层融合不会导致性能下降，但是可以减少网络参数

(ii) 在网络的后卷积层空间融合比浅层要好，另外在类别预测层融合会增加性能

(iii) 在时空邻域加入池化可以增加性能

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**e) Two-Stream 3D-ConvNet(**I3D**)（17.5）

## **实验**

本文选用的网络结构为**BN-Inception(TSN也是)**，但做了一些改动。如果2D的滤波器为N*N的，那么3D的则为N*N*N的。具体做法是沿着时间维度重复2D滤波器权重N次，并且通过除以N将它们重新缩放。训练的时候将每一条视频采样64帧RGB和64帧flow，经过3D卷积网络分别独立训练，最终计算平均值。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

在**miniKinetics**预训练的模型上操作效果如下：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

对于I3D的效果为什么好，作者解释说I3D有**64帧的感受野**。可以更好地学习时序信息。再就是先用**ImageNet的模型做了预训练**。

如果只使用**UCF101**数据集训练，准确率并不高，与TSN差不多

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **思考**

- 充分利用ImageNet ，训练时以pretrain初始化

- I3D加入更好的融合方法，不仅是简单的分类结果平均相加

- 3D+LSTM？

  #### 参考

[I3D github](https://github.com/deepmind/kinetics-i3d)