---
layout:     post
title:      What Actions are Needed for Understanding Human Actions in Videos?
subtitle:   人体动作识别需求分析综述
date:       2018-01-26
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition
---

## 摘要

**提出问题：**什么是推理人类活动的正确方式？前进的方向是什么最有希望的？

**解决思路：**目标是检查**数据集**，**评估指标**，**算法**和探索潜在的**未来方向**。

**得出结论：**论文分析活动的量化属性如：姿态变化程度，简洁性和稠密性。

- 物体和姿态细粒度的理解与时间推理相结合极大提升准确性。
- 展示了提升动作理解性能的需要信息：对象，动词，意图和顺序推理。
- 软件——提供其他研究人员详细诊断了解他们自己的算法。

Two-Stream (Simonyan & Zisserman 2014)

![clipboard(13)](/../img/2018-01-26-What-Actions-are-Needed-for-Understanding-Human-Actions-in-Videos/clipboard(13).png)

Multiple Algorithm Comparison

![clipboard(14)](/../img/2018-01-26-What-Actions-are-Needed-for-Understanding-Human-Actions-in-Videos/clipboard(14).png)

*1,2,3,4* 表示连续变量的程度 *N*,*Y* 表示是或否. 蓝色表示分类，棕色表示定位

## **现在的动作识别方法在学习什么？**

图中所有的数据点（x,y）x表示类别属性，y表示该类别的分类性能。

### **1.错误类型分析**

![clipboard(8)](/../img/2018-01-26-What-Actions-are-Needed-for-Understanding-Human-Actions-in-Videos/clipboard(8).png)

**相似类混淆（V+O:共享动词和名词 Object/Verb complexity）**

当一个类别与其它类别拥有越多的相似动作或物体，此类的准确性越低。图3表明错误分类的前几项类别特征：TP、BND（轮廓很少）OTH/FP没有此类OBJ\VRB(共享动作或物体)

**结果表明：在包含相似动作或者物体的不同行为间，细粒度的区分判别很重要**

### **2.训练数据分析——关注非平衡数据与准确率**

虽然更多的数据带来更高的准确率，但是更大的类别尺寸和更好的准确率并没有明确的相关性（P<0.1）（Charades数据集中每一类的样本数量不同）

![clipboard(4)](/../img/2018-01-26-What-Actions-are-Needed-for-Understanding-Human-Actions-in-Videos/clipboard(4).png)

#### **包含少量样本的类别:**

- 同时去除50%数据，绝对数量上大类别损失数据多，但是实际结果中小类别准确率下降更严重
- 同时限定每个类别有100个样本，相似类别准确率下降明显

#### **包含大量样本的类别:**

- 样本增多，姿态多样性并没有随之线性提升（无效性）
- 包含的相似物体增多，类间差异性变小（相似类混淆）

**结果表明：许多优化模型的本质在于提升小类别的准确率，将大类别切分为子类别进行识别也许效果更好（原子行为识别）**

### 3.基于人体推理

动作识别是应该基于图片还是基于人体，应不应该特别教会机器什么是“人”？

**图片帧人体定位（Faster-RCNN）：**

a）中等大小的人识别效果最好

b) 移除人比移除背景效果更糟，重新训练剪裁过只有人的图片效果更好

![clipboard(7)](/../img/2018-01-26-What-Actions-are-Needed-for-Understanding-Human-Actions-in-Videos/clipboard(7).png)

**姿态差异性分析**

![clipboard(9)](/../img/2018-01-26-What-Actions-are-Needed-for-Understanding-Human-Actions-in-Videos/clipboard(9).png)

姿态差异性：不同类别之间根据相应关节点距离计算的的姿态差异

姿态多样性上升，准确率提升

姿态相似性与测试时类间模糊有关

**结果表明：以人为重点的推理对于当代算法是有益的，姿态分析尤为重要。**



**未来研究方向**

**1.完美物体预测（object）**

假设与人体交互的物体在测试视频中全部给出

**2.完美动作预测（verb）**

假设与人体执行动作在测试视频中全部给出

**3.完美时间预测（Temporal）**

**4.完美意图预测（Intent）**

**5.完美姿态预测（Pose）**

![clipboard(3)](/../img/2018-01-26-What-Actions-are-Needed-for-Understanding-Human-Actions-in-Videos/clipboard(3).png)

**a)比较不同的完美预测**

**通过比较不同方向提升的空间，结果表明物体理解（object）更能提升准确率**

**b)比较不同方法**

增加时间信息对Two-Stream帮助最大，因为除光流之外双流法没有任何关于时间的信息而对于IDT帮助最小，表明其包含的轨迹信息已经能获取到高层时间信息

**c)比较不同数据集**

## **总结反思**

1.基于人像的训练使得动作识别准确率更高，可能也是光流成功的原因之一。因此在预测之前先进行人体分割是否会更佳？

2.时序信息很重要，小波变换并没有能够掌握到时序信息，采用其他动态信息弥补（IDT）

3.全局观念——基于视频而不是帧的训练与预测。TSN是将视频分段，然后根据图片帧进行分类融合，这就要求每一帧都要归属到对应的类，ActionVLAD提出一种全局的时空融合特征，不要求每一帧的分类完全准确（待研究）