layout:     post
title:      Attention Clusters Purely Attention Based Local Feature Integration for Video Classification
subtitle:   人体动作识别论文阅读
date:       2018-06-01
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition

## **摘要**

本文抛弃时序线索，探索**完全基于注意力**的方法来完成视频分类任务。在提出具体模型之前，作者分享了视频分类任务中局部特征几个重要特性：

**1.高度一致性**

慢动作类别（打高尔夫球），相邻帧存在大量信息冗余，将历史帧图片看做整体而忽略随时间演变的具体细节

**2.局部可辨别**

个别帧代表了分类需要的全部信息（刷牙），无需详细考虑时序关系

**3.无序性**

顺序对于视频分类并没有很大影响

![clipboard(13)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(13).png)

**抛弃时序线索，探索基于注意力方法的潜力——注意力模型本身优点**

- 注意力模型本质上为加权平均

- 少数关键帧给予更多权重——符合局部可辨别特性

- 注意力模型的输入是不同尺寸的无序特征集合，增加了应对不同数量局部特征的泛化能力

## **模型分析**

**局部特征提取+局部特征融合+全局特征分类**

### **Attention**

Local Feature Set 采用TSN架构采集局部特征CNN最后一层输出（L=7）

经典的TSN模型a=average(X),但是作者提出采用attention函数进行加权平均

![clipboard(1)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(1).png)

![clipboard(2)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(2).png)

![clipboard(8)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(8).png)

![clipboard(6)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(6).png)

### **Attention Clusters**

单一的注意力单元只能反映视频的某一方信息，会有很多信息被丢弃。因此为了表达视频中的多元信息，我们需要多个注意力单元来关注不同局部特征。

注意力簇：有着同样的输入数据但是内部权重参数不同的注意力单元群组

![clipboard(7)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(7).png)

![clipboard(15)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(15).png)

#### **Shifting Operation：**

单一的注意力模型丢弃了大量信息，而简单的将多个注意力单元输出拼接，由于每个注意力单元权重相差不大，只能得到微弱的提升，效率不高。于是提出了“**位移操作**”来有效增加注意力单元的多样性，提高识别准确率

![clipboard(17)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(17).png)

线性变换：改变了在特征空间的权重总和，彼此差异更大，有着不同的分布

L2归一化：尺寸无关性增强泛化能力，提升了整个网络的性能

#### **Overall Architecture**

增加了**多模态的融合**：每个模态单独训练一个注意力簇，最后进行整体融合

![clipboard(12)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(12).png)

## **实验分析**

### **1.如何选择注意力函数**

**注意力函数选择：简单的加权平均or全连接层softmax单层FC1（多层）**

![clipboard(14)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(14).png)

![clipboard(9)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(9).png)

![clipboard(4)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(4).png)

![clipboard(10)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(10).png)

实验证明，具有多隐藏层的FCn，注意力簇数量上升后效果开始下降。可能是由于数据饱和引起了过拟合。因此综合考虑效率和速度，选择一层FC1

### **2.如何选择注意力簇数量**

![clipboard](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard.png)

Table 3. Top-1 and top-5 accuracy (%) of multimodal integration of different cluster sizes for different modality on Kinetics.

### **3.聚簇方法有效性（位移操作shifting）**

![clipboard(16)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(16).png)

Table 2. Top-1 accuracy (%) on Kinetics to show the effect of different cluster sizes and training with (w/) or without (w/o) shifting operation for RGB, flow, and audio.

可以看出，如果不加位移操作，随着注意力单元个数的增加，性能一开始会提升，但是N>16之后趋于饱和或者下降，这是由于注意力单元之间的参数较为接近，但是计算量上升带来的过拟合现象更为严重。然而位移操作增加了簇间注意力单元的差异，提高了多样性，使得注意力单元簇更具有延展性。

![clipboard(5)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(5).png)

![clipboard(3)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(3).png)

![clipboard(11)](/../img/2018-06-01-Attention-Clusters-Purely-Attention-Based-Local-Feature-Integration-for-Video-Classification/clipboard(11).png)