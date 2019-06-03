---
layout: post

title: T-C3D  Temporal Convolutional 3D Network for Real-Time Action Recognition

subtitle:   动作识别论文阅读
date: 2019-01-17
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

- action recogniton
---

![clipboard(8)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(8).png)

## **摘要：**

2018 AAAI

本文的重要贡献在于提出了**实时动作识别**结构，利用分层多颗粒度的方式来表征视频中的动作。提出了新的**时序编码**的方式利用不同的融合函数来探究时序的动态特性。只用RGB模态测试时能达到准确率**91.8%**（UCF101）速度为**969fps**

## **模型：**

由于3D卷积能够很好的表征视频的动态信息，因此本文采用3D卷积来提取视频特征。然而3D卷积计算量较大，因此选用Res-18作为基网络来提取特征。（input =138*171）

T-C3D和以往工作不同的是计算视频层级的损失来更新训练，而不是采用视频片段或者单帧的预测结果。因此更能表达视频所有的信息。

**1.采取视频片段**

**2.用3D网络提取视频片段特征进行计算**

**3.利用融合函数计算片段得分得到视频最终结果**

**TSN VS T-C3D**

采用方法相当于光流图片（每个视频片段clip提取8帧图片），采用3D卷积提取特征。省去了额外提前光流的时间，故而提升了速度。并且实现了端到端的部署。

![clipboard(9)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(9).png)

![clipboard(2)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(2).png)

![clipboard](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard.png)

![clipboard(11)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(11).png)

![clipboard(5)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(5).png)

##  **实验：**

### **1.采取视频片段方式研究Evaluation on sampling methods**

当下流行的两种采样方式：

- 每段随机采样（不连续，平均间隔）
- 每段选择连续的几帧

![clipboard(3)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(3).png)

实验表明**连续采样RGB**更能匹和3D-CNN的特性，取得更好的效果（因为预训练模型的关系）既能得到RGB表观信息，也能捕捉动态信息

**2.视频片段数量研究Evaluation on snippets number**

![clipboard(6)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(6).png)

实验表明当视频片段数增加时准确率增加很多，但当数量从4增长后开始饱和，因此为了考虑速度，采用**clip=3**的方式。

### **3.视频片段融合方式研究Evaluation on aggregation functions.**

![clipboard(7)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(7).png)

实验发现在UCF101这样比较纯净的数据集中，简单的平均融合函数就能得到最好的融合方式，因此后文实验皆采用**平均融合**预测得分。

### **4.特征提取网络研究Evaluation on parameter initialization**

预训练的数据集越大越好？实验发现太大的数据集噪声多反而有干扰，**kinetics**数据集预训练 的结果最好。

![clipboard(4)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(4).png)

### **5.速度精度的研究Evaluation on the balance between speed and accuracy**

![clipboard(10)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(10).png)

![clipboard(1)](/../img/2019-01-17-T-C3D-Temporal-Convolutional-3D-Network-for-Real-Time-Action-Recognition/clipboard(1).png)