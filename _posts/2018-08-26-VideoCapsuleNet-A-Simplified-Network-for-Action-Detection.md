---
layout:     post
title:     VideoCapsuleNet: A Simplified Network for Action Detection
subtitle:   视频胶囊网络
date:       2018-08-26
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - action detection
---

# VideoCapsuleNet: A Simplified Network for Action Detection

![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/74DC96BAD9B64458B17B60179ADFC6EB/15924)

## **摘要**

本文是2018CVPR论文，提出了一个**3D胶囊网络(VideoCapsuleNet)**：一种用于**动作检测**的统一网络，可以联合执行像素级别的动作**分割**以及动作**分类**。

所提出的网络是从2D到3D的胶囊网络的推广，其将一系列视频帧作为输入。3D通用化极大地增加了网络中胶囊的数量，使胶囊路由在计算上花费很大，通过在卷积胶囊层中引入胶囊池化以解决这个问题。通过卷积胶囊层的参数化跳过连接进一步改善了本地化，网络通过分类和局部损失进行了端对端培训。所提出的网络在包括UCF-Sports，J-HMDB和UCF-101（24类）在内的多种动作检测数据集上实现了最先进的性能，UCF-101的改进效果大大提高了约20％，提高了约15％在v-mAP分数方面在J-HMDB上。

## **模型**

**胶囊池化（Capsule-Pooling）**

相比传统的卷积和池化，胶囊转换和路由计算复杂，使得3D胶囊网络的生成较为困难。因此当使用像视频这样高维度输入时，需要优化路由过程。

作者提出了一种新投票过程，应用于卷积胶囊层来减少胶囊路由过程中的计算数量。

1.同类型胶囊间共享转换矩阵参数

2.减少参与路由的投票数量

对于3D卷积胶囊，在L+1层的感受野维度是：(KT ;KX;KY )，Mc和ac分别代表拥有一个位置矩阵和激活的胶囊。

![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/AC51330277494ED8B695DDB82A29E583/15945)



![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/7D7A4243C9B1477DBA2418123907323B/15985)

### **定位网络**

想要获得帧级的动作定位，需要在类胶囊层的字体矩阵中学习动作的表达方式。在训练过程中，我们将屏蔽了所有姿态矩阵，除了真实标签类别，将其设为0。这样就可以让所有的类胶囊在真实标签处有一个最大的激活。（one-hot独热激活）

为了保证位置信息参与到最终定位，在卷积胶囊层使用了跳跃连接。

![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/0991A76819044C499E94EEFE89BE0978/15953)

### **目标函数**

![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/530230B744D441D38656FB3AF5619A4D/15997)



![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/4579A7FEC6A34487B977C10D334FC217/15999)



![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/AF958E6B11F44FE88785E95760DE4864/16001)

## **实验分析**

本论文实现代码框架采用Tensorflow，预训练模型是C3D，基于Sports-1M，UCF101等数据集验证。

![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/622400C1F7944085A61AB818E5303CC8/16032)

**类胶囊可视化：到底学习到了什么？**

![img](https://note.youdao.com/yws/public/resource/f1dfb6bffcf5ebe21c306d62d93bd591/xmlnote/DCBC6E264D554500BA2C7BEE1927C99E/16042)

以上可视化图片表明：类胶囊包含了特定动作信息并且与位置相关



## **总结思考**

- 本文研究长视频中行为检测问题，是行为识别分类的前提，也更具有难度。
- 胶囊自Hinton提出应用于图片分析后，视频也逐渐尝试这种新方法，只不过之前限于计算量过大，流程太长而没有研究成果。这片论文通过胶囊池化和类胶囊跳跃连接等方法，实现了在行为检测的同时，解决行为定位和行为识别的问题（主要是检测，附带了定位的功效）
- 识别领域的论文更新速度放缓，长视频、行为检测、视频理解等方向正成为视频研究领域的重点方向。