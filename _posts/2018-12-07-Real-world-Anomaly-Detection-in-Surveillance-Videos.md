---
layout: post

title: Real-world Anomaly Detection in Surveillance Videos

subtitle:   动作识别论文阅读
date: 2018-12-07
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- action recogniton
---

![img](https://note.youdao.com/yws/public/resource/06afc43f2b60191aa76807ac36b48f21/xmlnote/CCAC6F0F6326456CBF439FDB1EDC861D/20584)

## **摘要：**

异常事件检测任务（Anomaly detection)这个任务有许多的难点，比如：

1.异常事件发生的频率很低，导致数据的收集和标注比较困难；

2.异常事件的稀少导致训练中的正样本远少于负样本；

UCF的CV研究中心就在CVPR18上的论文，提出了一种基于**深度多实例排序的弱监督**算法框架，同时提出了一个新的大规模异常事件检测数据集**UCF-Crime**。

## **模型：**

### **1.弱监督**

- 设定正常模式，违背正常即为异常。但正常行为内包含太多类
- 设定异常模式，匹和异常模板即为非正常。但异常行为可能有很多未知

此处弱监督指在训练时，只知道一段视频中**有或没有异常事件**，而异常事件的种类以及具体的发生时间是未知的。

异常事件检测任务应该采取两阶段的框架，类似于目标检测中RCNN方法。

### **2.深度多实例排序**

**多实例排序****(Multiple Instance Learning)**机器学习领域中提出的方法：MIL中，“包”被定义为多个示例的集合，其中”正包“中至少包含一个正示例，而“负包”中则只有负示例

proposal：MIL的目的是得到一个分类器，使得对于待测试的示例，可以得到其正负标签

对于长序列视频中，异常行为只占很小一部分，MIL方法很适合。

每个视频均匀分为32个片段，作为一个包。训练时，随机选取30个正包和30个负包作为mini-batch进行训练。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

将异常检测定义为一个回归任务，即异常样本（anormal）的异常值要高于通常样本（normal）。Va和Vn分别为异常和通常样本，f则为模型预测函数。排序损失定义为：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

在训练中对于正包和负包都只使用分数最大的样本来训练。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

损失函数定义为：hinge-loss的形式——让正负样本之间的距离尽可能远

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### 作者的改进：

**1.连续平滑性**：由于视频片段是连续的，所以异常的分数也应该是相对平滑的。

**2.稀疏性**：由于正包中的正样本（异常事件）比例很低，所以正包的分数应该是稀疏的。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **实验：**

1.分包装袋

2.提取特征（C3D）

3.MIL排序（训练3层FC）

**数据集说明：**

较大规模的异常事件检测数据集UCF-Crime

该数据集共包含13种异常事件。共有1900个视频，其中异常和通常视频各占950个。数据集划分方面，训练集包含1610个视频（800个通常视频，810个异常视频），测试集包含290个视频（150个通常，140个异常视频）。

 Abuse, Arrest, Arson, Assault, Road Accident, Burglary, Explosion, Fighting, Robbery, Shooting, Stealing, Shoplifting, and Vandalism

虐待，逮捕，纵火，袭击，道路交通事故，入室盗窃，爆炸，战斗，抢劫，射击，偷窃，入店行窃和破坏行为

可应用行为：

Shoplifting（50）

stealing（100）

burglary（100）

**异常事件分类**

该文的方法只是做异常事件proposal，但该文的数据集实际上还能做异常时间分类任务，所以此处作者还用C3D和TCNN两种行为识别算法跑了一个baseline，可以看出此处TCNN的效果还是比C3D要好很多。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



## **总结思考：**

**1.视频预处理：**分类准确率过低的原因在于长视频+复杂动作行为。嵌入式应用可以先进行视频剪裁（5s），挑选异常行为片段进行训练

**2.训练盗窃行为分类器（基于TSN迁移）**

**3.区分异常/正常行为**

**4.迁移至JETSON**