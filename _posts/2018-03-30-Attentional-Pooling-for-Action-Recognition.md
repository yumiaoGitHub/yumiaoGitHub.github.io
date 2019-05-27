---
layout:     post
title:      Attentional Pooling for Action Recognition
subtitle:   多模态信息融合
date:       2018-03-30
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- 算法
    - action recognition
---

# **[Attentional Pooling for Action Recognition](http://rohitgirdhar.github.io/AttentionalPoolingAction)**

Rohit Girdhar Deva Ramanan

The Robotics Institute, Carnegie Mellon University

## 摘要

这是2017年NIPS上的一篇做动作识别的论文，作者提出了second-order pooling的低秩近似attentional pooling，用其来代替CNN网络结构最后pooling层中常用的mean pooling或者max pooling, 在MPII, HICO和HMDB51三个动作识别数据集上进行了实验，都取得了很好的结果。



## **二阶池化**

**一阶池化：mean和max（一阶操作）**

![img](https://note.youdao.com/yws/public/resource/28ea88f88e2faf8e965acd644ab3af1d/xmlnote/117D3C987A2441E38059D60A08836644/9956)

**二阶池化：feature map中的每个向量与自身的转置求外积来实现的。**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**理论说明：**在[线性代数](http://zh.wikipedia.org/wiki/线性代数)中，n*n的[矩阵](http://zh.wikipedia.org/wiki/矩陣)A的秩是指A[主对角线](http://zh.wikipedia.org/wiki/主對角線)上各个元素的总和，一般记作tr(A)

**Tr()迹操作符来表示两个矢量的内积（结果是个标量：类别得分）**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



n = 16*16 = 256  f 表示通道数 (e.g. 2048)

一个很显然的问题是，二阶比一阶计算量要大，因为实际实现的时候，second-order pooling须用到矩阵相乘，计算量自然比矩阵求max或求mean要大。但是研究者发现在**语义分割和细分类**问题中，二阶pooling效果更好，因此为了效果提升，在某些情况下增加一些计算量还是值得的。

如果采用直接计算，对于2048*2048的W计算量太大，因此为了实际运用必须简化！

**简易实现——二级池化的低秩近似(low-rank)**

矩阵的秩：线性独立的最大数 r(W),rk(W),rank(W)

权重矩阵W进行秩为1的近似，将其表示为2个向量a和b的转置的乘积：W = abT

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

根据迹的性质：1.乘法交换律Tr(ABC) = Tr(CAB) 2.标量的迹为其本身推导如下

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



**Top-down attention 和 bottom-up attention**

以上公式推导是针对二分类问题的，对于多分类问题，只需要将参数W变为针对每个类不同的Wk即可，公式如下：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

对输入X，计算所有的score(X, k), k=1, 2, ..., N，N为类别数，寻找最大的score值，对应的k即为predict的类别。

同时，对Wk也可以进行一阶的近似，将其表示为Wk = ak * b，注意ak表示向量a是跟k有关的，而向量b是与类别k无关，

分辨模型：从上至下**类别确定**注意力机制+从下至上**类别模糊**视觉显著性机制

 top-down attention 是用目标驱动的方式来进行visual search，而 bottom-up 则是根据图像的显著性信息来进行visual search，这种分类方式也是**受启发于人脑激励方案：根据自上而下的线索调节关注显著性区域（自下而上反馈）**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

最终

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



## **实验结果**

### **注意力可视化对比：**

针对正确的类别，Bottom-up、top-down以及两者融合的热图均有明显的激活区域，而对于错误的类别，top-down的热图无显著的激活区域，该结果说明来自top-down的注意力能够正确的定位与类别息息相关的区域；

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



### **运动识别方法对比：**

HMDB51 dataset using only the RGB stream

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### **低秩近似实验对比：**

1.**满秩**：二阶pooling计算量太大，因此采用compact bilinear approach （CBP）来近似。

2.**P秩近似**：最极端的情况就是1秩近似，即将一个矩阵分解为2个向量相乘，此外还有2秩，3秩近似。P秩近似就是把矩阵分解为两个低秩矩阵，其中秩较大的矩阵的秩为P，作者采用P个bottom-up feature maps 和 C个 top-down feature maps 来相乘，发现在秩为1，2，5的时候，在MPII数据集上的mAP分别为30.3, 29.2和30.0, 可见结果对不同的秩还是比较稳定的。



## **总结思考**

1.借鉴脑科学的基本知识，提出在动作识别中引入注意力机制，采用简洁的实施过程，改善了原有的动作识别技术。

2.巧妙的将数组乘法通过低秩近似转化为两个注意力向量相乘：自上而下的类别注意力和自下而上的视觉注意力

3.[低秩近似](http://www.songho.ca/dsp/convolution/convolution2d_separable.html)：想起了MobileNet使用的可分离卷积

4.[矩阵低秩分解理论及其应用](https://wenku.baidu.com/view/7128ca3014791711cc791765.html)



### 参考：

[**Yunfeng's Simple Blog**](https://vra.github.io/2018/01/20/paper-attentional-pooling/)