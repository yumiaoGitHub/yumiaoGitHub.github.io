---
layout:     post
title:     Recognize Actions by Disentangling Components of Dynamics
subtitle:   人体动作识别论文阅读
date:       2018-08-10
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - action recognition
---

## **摘要**

这是商汤在2018 CVPR上第二篇关于从RGB生成光流相关的工作了。第一篇是OFF（利用RGB直接生成类似光流特征达到UCF101最好成绩96%）由于单独计算光流浪费时间和空间，效率很低，因此本论文直接从视频帧中获取动态信息，不需要额外计算光流。

## **模型架构**

![img](https://note.youdao.com/yws/public/resource/e3af9acf6cd1dd13af1040c94c3a1971/xmlnote/139ECE6322CB4DAE9A05A0876B4F7BFC/15019)

本文将动态表示分解分为三个部分： static appearance， apparent motion，appearance changes

### **1. 静态表观分支Static Appearance Branch**

这个branch主要是用来提取整个场景的静态表观特征的。它的结构主要包括2D conv，2D pooling和temporal pooling。 

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

时序池化是为了使特征更具有鲁棒性。由于某一帧特征可能受到运动模糊，相机抖动影响，时序池化把多帧的特征通过池化方法集合到一起则可以比较好地解决这一问题。

### **2. 动态分支Apparent Motion Branch**

这个branch表示的是视频帧上**特征点的空间位移**。一般工作中运动特征通常是通过密集光流场来表示的，但是光流的计算通常耗时很大。因此我们想出了一种替代的方案——cost volume。计算如下图：



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

在相邻帧的低层特征图上计算cost volume。给定一对feature map Ft和Ft+1，我们可以构建一个4维的cost volume Ct∈RH∗W∗(2ΔH+1)∗(2ΔW+1)，也就是说feature map上的每一个点都和其领域 (2ΔH+1)∗(2ΔW+1) 范围的所有点计算一个**相似度**。具体地，cost volume上的每个点Ct(i,j,δi,δj)是ft(i,j)和ft+1(i+δi,j+δj)的cosine similarity。

在得到cost volume后，再计算一个位移映射矩阵（displacement map）Vt∈RH∗W∗2来捕获t到t+1时刻的运动，在这个矩阵上的每个位置(i,j)都会得到一个2维的向量vi,j=(vyi,j,vxi,j)表示当前位置的位移，计算方式如下： 

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### **3. 表观变化Appearance Change Branch**

光照的变化，物体本身形状的变化等不能由apparent motion解释。在以往工作中通常用rgb-diff帧差来表示，但是和前一个分支有耦合，因此采用新思路——Warped difference

以往的工作中介绍warp flow可以改善光照、抖动等影响。给定相邻帧的特征图Ft和Ft+1，我们首先根据Vt对Ft进行warp，即根据之前计算的apparent motion得到估计的后一时刻的feature map F′t+1=W(Ft,Vt)，warp是通过**双线性差值**进行的。然后计算warped feature map **F****′t+1****和****F****t+1****之间的差**，即得到了warped difference。然后再将warped difference输入到后续的网络中得到1024维的特征向量。

## **实验分析**

**1.cost volume有效性**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**2.warp-diff有效性**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**3.各分支融合有效性**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**4.对比**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



## **总结思考**

1.通过神经网络模型计算光流（类似特征）是未来视频分析领域的发展趋势。

2.多分支融合仍然是目前提升准确率的常用方法，因此融合模型值得研究。

3.RGB-diff迁移至warp-diff算法的改进建立在对其本质有效性的思考，适当迁移方有创新。