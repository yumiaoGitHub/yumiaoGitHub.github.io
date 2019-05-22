---
layout: post

title: Improving Human Action Recognition through Hierarchical Neural Network Classification

subtitle:   动作识别论文阅读
date: 2018-11-23
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- action recogniton
---

![img](https://note.youdao.com/yws/public/resource/72c6ad0885318d952d13ec24d5d12571/xmlnote/5BAE48886F2946F69A110DCBC48D094E/20178)

## **摘要：**

本文发现人体动作识别准确率低的原因之一在于：针对于不同的动作类型使用相同的模型。对于小而类似的数据集，手工提取的特征更好，但是大而多样的数据集用训练出来的特征泛化性更好。如何将针对性和泛化性性有效的结合起来？

因此提出一种基于CNN的分层网络结构，应用在识别kinetics数据集上最难识别的（准确率最低）20类人体动作上。

## **模型：**

**1.分层softmax的思想应用在分类器上。**

**2.high-level、low-level信息**

**3.粗粒度-细粒度思想**

将一个大模型拆分为几个小模型，每个子模型只负责某一组动作（group-action）

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

分为两个步骤：

1.第一阶段：将输入视频分类为行为组（group）

2.第二阶段：进一步处理分析视频决定特定的动作类别（action）

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

每一个C分类器的网络结构：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

采用3D卷积和3D池化提取特征，只用了8层卷积。模型简单快速。每一个分类器的参数都不相同，因此可以更好的适应不同类别的动作。第一层softmax输出为9大群体（P=group）第二层的分类器为其包含的类别个数。

## **实验：**

### **1.准确率提升**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### **2.速度快易训练**

### 分组的方法极大的简化了网络的训练，网络结构简单，层数少（8+8）。而且由于分组之间的训练相互独立，不会相互影响，数据量小。

### **3.可扩展性强**

对于新类别的扩展灵活性很好。不需要改变整个大模型，只要改变某一个分组下的小分类器就可以。例如本文只实验了20个类别，如果想要拓展到400个类，粗粒度群分类器（high level）可以不用改变，对应细粒度分类器（low level）进行微调就可以更新。



## **反思：**

- **资源限制怎么办？**硬件资源不够，时间不充足的情况，选用大数据集的某一部分进行特定的问题解决。Kinetics准确率最低的20个分类（相似类别的分类）

- **粗粒度-细粒度**的思想值得借鉴。比如用在图网络的借鉴上

- **分层分类**的方法对于训练和预测速度都有很大提升，可以值得后续研究（虽然训练不是端到端，但是测速部署可以）