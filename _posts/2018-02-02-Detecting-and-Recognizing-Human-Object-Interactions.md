---
layout:     post
title:      Detecting and Recognizing Human-Object Interactions
subtitle:   人体动作识别需求分析综述
date:       2018-02-02
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition
---

# **[Detecting and Recognizing Human-Object Interactions](https://arxiv.org/abs/1704.07333)**

Georgia Gkioxari Ross Girshick Piotr Doll´ar Kaiming He

Facebook AI Research (FAIR)

## 摘要

本文主要关注图像中的 **人** 和 **物体**的关系检测和识别，这种关系可以用一个三元素 **<human, verb, object>** 来描述。使用了 Faster R-CNN 检测框架，对于含有人的一个候选区域 RoI，根据人的**表观特征**，进行 **动作识别** 和 动作关联的物体位置的 **密度估计**（高斯分布）。

![clipboard(2)](/../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard(2).png)

## **模型框架**

![clipboard(6)](/../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard(6).png)

### **1.Object Detection branch**

这个分支和 **Faster R-CNN** 完全一样，使用 Region Proposal Network (RPN) 提取候选区域，然后进行分类和矩形框坐标回归，得到人和物体的类别及位置矩形框和对应的概率，在 inference 是只是用检测出人和物体的候选区域，在训练时使用RPN提取的所有候选区域.

![clipboard(3)](/../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard(3).png)

### **2.Human-centric branch**

- #### **Action Classification**

human-centric 分支的第一个任务就是对每一个人（human） 和 动作（action） 赋予一个 动作类别得分（score)，人可以同时进行多种动作，所有我们这里进行多类别分类。

- #### **Target Localization**

human-centric 分支的第二个任务就是基于人的 appearance 预测相关联物体的位置，直接预测位置难度较大，这里我们给出物体位置的密度概率。

可看出，即使物体和人无交叠（**IoU=0**）通过人体姿态分析依然可以识别出关联物体。

![clipboard(1)](/../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard(1).png)

![clipboard(11)](/../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard(11).png)

### **3.Interaction Recognition branch**

为了提高模型的表达能力，进一步利用了 相关物体的表观特征（object appearance）

![clipboard(3)](../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard.png)

**多任务学习&层次预测**

三个分支是共同训练。Loss=L1+L2+L3

我们使用了 Cascaded 来降低时间复杂度：**Object Detection branch输出结果作为Human-centric branch的输入。**关键是**只对人的矩形框**进行相关处理实现 ∼ 135ms

**三元组：<human,verb, object>   <bh,a,bo>**

## **实验结果**

- 成功的检测到人体预测框之外的交互物体
- 一个人可以进行多种动作、关联多种不同的物体

![clipboard(4)](/../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard(4).png)

![clipboard(5)](/../img/2018-02-02-Detecting-and-Recognizing-Human-Object-Interactions/clipboard(5).png)

