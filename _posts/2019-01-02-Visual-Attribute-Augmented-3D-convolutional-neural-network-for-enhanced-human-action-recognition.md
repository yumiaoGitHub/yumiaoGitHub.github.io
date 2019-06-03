---
layout: post

title: Visual Attribute-Augmented 3D convolutional neural network for enhanced human action recognition

subtitle:   动作识别论文阅读
date: 2019-01-02
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- action recogniton
---

![clipboard(7)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(7).png)

## **摘要**

虽然3D网络结构在视频动作识别任务中取得了优异的性能，但是由于**缺乏对视频中的视觉属性的显式的学习**，因此对于某些空间整体模式和时间运动模式上很相似的视频类别，3D卷积网络无法区分出来。

![clipboard(4)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(4).png)

为了解决这个问题，我们提出了基于视觉属性挖掘，(Visual Attributes Mining)的增强3D网络(A3D，Attributes-augmented 3D CNN)将利用成熟的物体检测算法和自然语言处理领域中的算法来从视频中发现有用的视觉属性，然后将视觉属性和视频关联起来，对视觉属性进行网络进行识别，实验表明我们的方法在引入很小的额外计算开销的情况下，能够提升视频中的动作识别准确率。

## **模型**

**视觉属性的发现和识别**

视觉属性，我们定义为跟视频中的**动作相关的人体或者物体**，带有明显的语义信息。对于绝大多数动作类别，视觉属性具有明显的区分性，对视觉属性的挖掘和利用能够有效地提升视频动作识别的准确率。

**1.初选物体：**首先，我们从每个视频中选取一帧ＲＧＢ数据作为样本，将样本输入到由darknet网络组成的YOLO-9000，获取可能的物体区域和对应物体的标签。物体区域信息包括检测到的物体的在图像中的左上角坐标和长和宽，标签信息即为该区域的类别。（删除了宽和高都小于20厘米的区域，包含视觉属性的概率较小，大部分是噪声。有效地去除跟视频动作识别不相关的区域信息）

**2.Word2Vector：**将剩余物体区域的标签信息，以及视频的类别信息输入到词向量。

**3.过滤训练：**计算剩余物体区域的类别和视频的类别这两个向量间的距离，由于词向量间的距离越小，词的语义相关性越高，保留距离值小于某个阈值的物体区域，删除距离值大于阈值的物体区域。通过这步操作我们期望视觉属性候选区域中跟类别最相关的部分，而删去相关性不高的部分。经过过滤操作，剩余的物体区域我们就称为视觉属性。

![clipboard(3)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(3).png)

### **A3D整体结构**

三路子网络，上面两路网络是I3D中的光流网络和RGB网络，分别通过3D版本的Inception-V1结构，最后得到的结果进行加权相加。最下面一路是本课题提出的基于视觉属性发掘的改进方案。

![clipboard(6)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(6).png)

联合推理(T=0.1)

![clipboard(9)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(9).png)

![clipboard(1)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(1).png)

## **实验结果**

### **1.实验优化**

![clipboard(8)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(8).png)

特征融合方法在UCF101数据集上有1.48％的性能提升，在HMDB51上也有0.22％的性能提升。在更前面的层进行了特征融合，因此包含更多的低层信息，这些信息相比最后的概率值，有更好的表达能力。

### **2.精度提升**

![clipboard(2)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(2).png)

A3D多了视觉属性检测和分类的一路，因此增加了一些额外的时间开销。但是发现额外的时间开销所占比例比较小，在训练集上额外时间开销分别占8.76％和6.71％，而在测试集上额外时间开销增加不到２％。

### **3.可行性体现**

![clipboard(5)](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard(5).png)

![clipboard](/../img/2019-01-02-Visual-Attribute-Augmented-3D-convolutional-neural-network-for-enhanced-human-action-recognition/clipboard.png)

## **总结反思**

**视觉显著特性——注重物体和上下文信息**

在探求动作相关的人体或者物体的步骤更为精细：选用更大的物体检测网络YOLO-9000识别更为细致。通过区域筛选去除小物体区域来去除噪声，通过word2vec词向量相似度选择类别相近的物体候选框。最终将词向量通过分类网络实现动作识别的分类。