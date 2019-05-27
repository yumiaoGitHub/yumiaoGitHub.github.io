---
layout:     post
title:     A Closer Look at Spatiotemporal Convolutions for Action Recognition
subtitle:   人体动作识别论文阅读
date:       2018-06-29
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - action recognition
---

## **摘要**

本文讨论研究了针对于人体动作识别人物，几种时空卷积网络结构。对比分析了2D卷积，3D卷积，2D3D混合卷积，因式分解3D卷积（2D空间+1D时间），实验验证R(2+1)D分解的3D卷积效果最好。

## **整体结构**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

针对于视频理解任务，时序信息是非常重要的线索。然而传统的2D卷积无法充分挖掘出时序信息，3D卷积由于数据量过大又难以推广。I3D的提出在各个数据集上都达到了最高成绩，因此很多学者开始探讨如何有效3D卷积的强大功效。本论文以ResNet网络为骨架，列举对比了5中结构：

### **（a）R2D：纯2D卷积**

R2D将L帧，宽高分别为W，H的一个视频clip当成3LxWxH的3D tensor输入网络，得到的还是3D的tensor。虽然是3D tensor，实际的卷积是2D卷积，因此时间信息是全部丢失了

### **（b）MCx：混合2D和3D卷积**

**将第x以及后面的3D卷积换为2D的卷积**

有一种观点认为卷积网络较低层对motion的建模比较好，而高层由于特征已经很抽象了，motion和时序信息建模是不需要的，因此作者提出了MCx网络，即将第x以及后面的3D卷积group换为2D的卷积group

### （c）rMCx：混合2D和3D卷积

**将第x以及后面的2D卷积换为3D的卷积**

同时还有一种假设认为高层的信息需要用3D卷积来建模，而底层的信息通过2D卷积就可以获取，因此作者提出了rMCx结构，前面的r代表reverse，即反向的意思。rMCx表示前面的5-x层为2D卷积，后面的x层为3D卷积。

### **（d）R3D：纯3D卷积**

C3D第一次提出，I3D推至巅峰

### **（e）R(2+1)D：分解3D卷积为2D空间卷积+1D时间卷积**

3D到(2+1)D的拆分有下面两个好处：

1.增加了**非线性**的层数（通过ReLU来得到）

2.使得网络优化更容易，网络更**易于训练**。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **实验分析：**

### **1.不同网络结构性能分析**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

- 纯2D网络比含3D的网络（包括R3D, MCx,rMCx, R(2+1)D）性能要差
- MCx性能优于rMCr，因此说明在网络底层的3D卷积层更能提取时间信息，而后面用2D卷积能减少计算量，更为合理。
- R(2+1)D性能最好，是后面实验的卷积结构

### **2.不同长度Clip选取分析**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

以TSN中光流为例，一个视频分为3个clip,每个clip取L=10张连续图片。作者采用了8，16，24，32，40和48帧的clip进行实验，对clip-level的结果和video-level的结果进行分析，得到的准确率如Figure 5所示。可以看到，clip-level的准确率随着clip的长度增长在持续上升，而video-level的准确率则在**24帧**的时候达到最高，后面反倒有所下降，作者分析随着clip长度的增加，不同clip之间的相关性增加（甚至可能会产生重叠），所以video-level的准确率增益越来越小。

Comparison with the state-of-the-art on Kinetics

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### Comparison with the state-of-the-art on UCF101 HMDB51

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

单独RGB和Flow的准确率都比I3D高，但是双流混合起来效果不如I3D，可能互补性不够

## **总结反思：**

作者的主要贡献有两个：研究了2D3D融合卷积网络，并实验验证了MCx比rMCx好，先用3D提取时空特征，再用2D的策略更合理。提出了新的卷积结构R(2+1)D，是准确率和计算量的完美结合。

1.可以将我们的**卷积网络换成R(2+1)D**，虽然计算量增加了，但是相比于3D卷积更容易训练，而且预期的性能结构会更好

2.**增加clip里面的帧数**。传统光流都是采用L=10，而这里的实验验证，增加L=24可以有效提高网络对应时序信息的利用。

3.**将R(2+1)D与融合网络结合**，因为从kinectics数据集测试结果来看，RGB和光流的每一项的都比I3D 高，说明单独的分类器都训练的很好，但是以1:1简单相加互补性并不好。如果采用我们的有注意力多模态融合网络，效果应该得到进一步提升