---
layout:     post
title:      End-to-end Video-level Representation Learning for Action Recognition
subtitle:   人体动作识别论文阅读
date:       2017-12-28
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition
---

## 摘要

不同层级的特征表达具有不同的优势，本文则创新提出了一种视频层级的动态特征表达

- Hand-crafted video representation（iDT）
- Frame-level feature by deep learning(LSTM,C3D,TSN )
- Video-level representation by deep learning

## **网络模型**

![img](https://note.youdao.com/yws/public/resource/21de8415a5f138992a565aea0fed39a7/xmlnote/32177573011E4A11B66A5748AEE5B7EE/6576)

橙色表示空间信息流（RGB作为输入），蓝色表示时间信息流（flow作为输入）类似TSN

### 稀疏采样

TPP层将帧层级信息聚合为视频层级的表达方式。最后，两个信息流通过加权平均的方式融合

Si为通过Inception卷积层后的特征向量

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**实验结果得出：以Max Pooling kernel方式的三层TPP 效果最佳！**

### **Temporal Pyramid Pooling (TPP) layer**

把经过全局池化的inception-5b作为TPP输入，有 T=25个（采样帧数）1\*1024的特征向量

我的预测：二分法取最大值

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### **对比：Spatial Pyramid Pooling**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### **采样帧数的影响**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

由于two-stream ConvNets and TSN在预测时需要late fusion，在测试时也需要固定的输入尺寸。而DTPP可以推广到较为广泛的情况。可以看出，在15-50帧采样的情况下精确度相差不大。

我们即将与全连接层连接的时候，就要使用金字塔池化，使得任意大小的特征图都能够转换成固定大小的特征向量，这就是空间金字塔池化的奥义（多尺度特征提取出固定大小的特征向量）

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)





### 参考

[ＳＰＰ](<http://blog.csdn.net/u011534057/article/details/51219959>)