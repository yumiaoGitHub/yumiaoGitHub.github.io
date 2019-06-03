---
layout:     post
title:     Compressed Video Action Recognition
subtitle:   人体动作识别论文阅读
date:       2018-07-27
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - action recognition
---

# **Compressed Video Action Recognition**

## **摘要**

本文由多个高校合作而成，是CVPR 2018  oral 文章，研究从压缩视频中直接提取特征。训练鲁棒的深度视频表示比学习深度图像表示更具挑战性。这部分是由于原始视频流的巨大数据量和高时间冗余; 真正有用的信号经常被淹没在太多不相关的数据中。 由于视频压缩（使用 H.264、HEVC 等），多余的信息可以减少多达两个数量级，因此本文提出直接在压缩视频上训练深层网络。 这种表示具有更高的信息密度，实验证明训练也会更加容易。另外，压缩视频中的信号提供直接的但包含很大噪声的运动信息。 本文提出新的技术来有效地使用它们。 提出的方法比 Res3D 快 4.6 倍，比 ResNet-152 快 2.7 倍，**兼顾了速度与精度**。 在动作识别的任务上，本文的方法优于 UCF-101、HMDB-51 和 Charades 数据集上对应的其它方法。

## **模型架构**

**传统的**网络架构是把压缩的视频流**先解码**，**再切片**为帧图片，选取关键帧投入网络进行特征提取，训练得到结果。本文则直接使用压缩视频流。

![clipboard(10)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(10).png)

### **压缩表达建模 Modeling Compressed Representations**

视频压缩简介

一般算法：MPEG-4, H.264, and HEVC

关键术语：I-frame(包含大部分信息的压缩图片帧intra-coded frames) P-frames(记录与前一帧变化的动作向量predictive frames) B-frames(与前后帧的变化bi-directional frames)

![clipboard(5)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(5).png)

![clipboard(8)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(8).png)

本文主要利用视频压缩的MPEG4格式中的I-frames、Motion Vector和Residual三部分来做视频动作识别。在精度上优于主流算法，主要原因是避免了视频转为连续RGB图像后生成时序上大量冗余的信息，但仍旧能够在MP4中捕获对训练有用特征；在效率上也显著优于主流算法，原因是直接输入MP4格式省去了大量预处理时间，比如视频转换时间，提取光流时间等等。由于单独的输入类似于光流的P帧图片只记录了两帧图片间的差异，信噪比很低难以建模，因此**考虑长程差异用积累动作向量和残差**，会显示出更加清晰的模式。

### **打破P帧依赖Decoupled Model**

![clipboard(4)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(4).png)

![clipboard(3)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(3).png)

把MP4视频格式中I-frame和P-frame的逐帧依赖方式给打破了，这样跟有利于网络的并行训练。具体的网络结构如下：

### **提出网络Proposed Network**

![clipboard(9)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(9).png)

网络的实现是参考TSN基础架构。由于最主要的信息集中在I帧，因此用ResNet-152对I帧建模，P帧记录了更新变化，使用简单模型就满足，选用ResNet-18进行建模来捕捉帧间更新信息。这样同时兼顾了速度和精度。

## **实验分析**

**1.探究积累特征的有效性**

![clipboard(7)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(7).png)

**2.网络复杂度和效率比较**

![clipboard(2)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(2).png)

![clipboard(1)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(1).png)

**3.准确率比较**

![clipboard(6)](/../img/2018-07-27-Compressed-Video-Action-Recognition/clipboard(6).png)

## **总结反思**

**1、压缩的视频进行特征提取**

对比之前的论文 [A Closer Look at Spatialtemporal Convolutions for Action Recognition](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1711.11248.pdf)结合2D卷积和3D卷积去处理视频动作识别任务的思路，本篇论文最大的创新点在于直接采用压缩的视频进行特征提取，新颖高效。

**2.充分结合静态信息+动态信息**

计算光流十分耗时，但视频压缩中P帧中Motion Vector一定程度上与光流相似，因此可以不利用光流的情况下，也能保证一定的精度。

**3.探究有技巧的多特征融合**

特征上利用积累的Motion、Residual和I-frame，I-frame集中了大部分信息，采用复杂网络，而motion则采用简单网络，物尽其用，最优化效率。

**4.小波变换类比**

这种思想和我之前尝试过的小波变换类似：LL（I帧）代表了集中大部分能量的低频信息，HH（P帧）则代表了轮廓边缘和噪声信息。当时虽然减小了计算量提升效率但是损失了准确率。可以参考本文的思路实现准确率和效率的兼顾。