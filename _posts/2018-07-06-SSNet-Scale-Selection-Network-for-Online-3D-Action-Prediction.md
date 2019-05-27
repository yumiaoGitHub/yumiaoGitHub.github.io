---
layout:     post
title:      Scale Selection Network for Online 3D Action Prediction
subtitle:   人体动作识别论文阅读-SSNet
date:       2018-07-06
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - action recognition
---

## **摘要**

本文讨论研究了3D骨架序列信息的在线行为预测问题。作者参考了滑动窗口sliding window的思想提出了**窗口尺寸选择方法**，运用**扩张卷积**在时间序列上自适应选择合适的帧图片结果。主要贡献有：

（1）研究了在线行为预测问题，第一次在时间维度运用卷积分析

（2）提出SSNet能够处理在不同时间尺度上观测行为的尺度变化问题

（3）在不同时序步骤中运用共享计算减小了计算复杂度

（4）实现了端到端的训练而不是多步骤和多网络的方法

## **整体结构**

UCF101和HMDB51都是剪裁好的视频片段，每一个视频只有一个动作类比，但是对于现实生活中的应用，一段视频流可能包含多种行为，并且每种行为的时间长度不一，因此未分割视频的在线行预测问题更具有价值和挑战性。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

Fig(b)展现了SSNet应用在3D行为预测。t时刻，智能观测到挥手的部分信息，相比于第三层的卷积结果，**SSNet会倾向于选择第二层的卷积结果**。由于第二层的的卷积（时序窗口）包含了当前行为部分，而第三层卷积对应的时间窗口包括了来自前一个行为的信息，会影响当前t时刻的行为判断。

**SSNet是用来选择合适的时间窗口大小**，能包含当前行为的其他图片帧信息，同时抑制来自前面行为的噪声信息影响。因此在每一步都能产生可以信任的预测结果。

作者说明：有生物研究表明，**骨架信息足够丰富能代表人体行为**，甚至不需要表观信息，人体行为一般在三维空间，因此3D骨架信息非常适合用于人体行为的研究。

### **利用卷积时序建模**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

每一步，SSNet会预测当前行为的**类别Ct** 和当前行为距离初始点的**距离St**，每一层的卷积参数共享。

### **尺度选择方法**

具体的Ct和St计算过程如下图：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

如果上一时刻t-1的回归结果St-1表明第三层是最优时间窗口尺度，那么我们的网络则会使用1-3层的应用于类比预测，**在3层以上的则会被抛弃不被激活**。（层级越高，包含了更长时间序列的信息，这里的网络可以找到尽可能多的包含当前行为的图片帧信息，从而避免信息遗漏和冗余）

### **目标函数**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



## **实验分析**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

SSNet检测速率达到了**70fps**表明该结构响应很快。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

SSNet-GT表明Ground Truth scales，代表了最理想的预测结果，这里SSNet非常接近理想结果，也说明了该方法利用自适应时间滑动窗口，重复挖掘了时序信息。

## **总结反思**

（1）这是第一篇在时间序列上运用卷积分析的文章，具有创新性。

（2）将回归的思想和分类结合，有效解决了真实视频序列的行为预测问题。

（3）将目标检测中滑动窗口的思想（空间滑动）迁移至时序滑动，并且设计网络是的滑动窗口的大小可以自适应改变，传统的方法采用固定的值或者几个多尺度，这样计算量大且包含冗余信息。本方法计算量小且抑制了前面多余信息的干扰，解决了准确度和效率问题。

（4）3D信息，骨架信息都是行为识别输入信息的另一种模态，无论是特征提取还是特征融合，都是可以继续研究的方向。

（5）单纯的分类问题结合回归（跟踪，预测等）可以具有更广阔的应用价值。