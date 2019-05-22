---
layout: post

title: Temporal Shift Module（TSM）

subtitle:   动作识别论文阅读
date: 2019-03-15
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- action recogniton
---

![img](https://note.youdao.com/yws/public/resource/7f1729ff4842a66b2246b61b1dc3d14b/xmlnote/23C5605AF3224A3AAF167B043E0E3B43/22119)

## **摘要：**

针对在线视频理解问题，2D CNN计算简单但是不能捕捉长程时序关系，3D CNN能取得较好的效果，但是计算量大难以部署。本文提出了一种通用且有效的方法：时间位移模块TSM，以2D的计算复杂度达到3D的准确率。

TSM中心思想：时间维度通道的移动，使得相邻帧间信息进行交换。更关键是，在2D CNN 中嵌入TSM模块取得时序信息，只需要简单的位移操作，这不会带来任何的计算量。实验结果表明比ECO参数少3倍，模型小了3倍。

![img](https://note.youdao.com/yws/public/resource/7f1729ff4842a66b2246b61b1dc3d14b/xmlnote/B0362B1AC2EA4A399E9CB4C309243B56/22113)

## **模型：**

### **时序建模**

经典常用：3D CNN、LSTM

启发：一般的卷积操作，可以分解成 位移shift + 权值叠加 multiply-accumulate 两个过程。比如说对一个1D vector X 进行 kernel size=3 的卷积操作 Y = Conv(W; X) 可以写成：

![img](https://note.youdao.com/yws/public/resource/7f1729ff4842a66b2246b61b1dc3d14b/xmlnote/AE18C1AEAC1341D1A0B8A2D41F10450D/22144)

故分解后的两个操作分别为：

- 位移（基本不消耗计算资源，常规地址偏移指针操作）

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

- 权值叠加

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

设计TSM模块时候，尽可能多使用位移操作（几乎0计算量），把权值叠加操作放到2D CNN本身的卷积里去做，这样就可在不加任何参数计算量基础上，实现更多功能。

### **TSM模块：**

时空建模的视频理解任务里，如何利用位移操作呢？



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

上图中最左边的二维矩阵是 Ti; 

中间是通过TSM模块位移后的的矩阵，可见前两个channel向前位移一步来表征Ti-1 的 feature maps，而第三、四个channel 则向后位移一步来表征 Ti+1 ,最后位移后的空缺 padding补零；

右边的与中间的类似，不过是 循环方式来补齐，后面实验会对比两种不同padding方式的性能差异。

## **整体框架：**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

基础网络是ResNet-50，且在每个 residual unit 后都会加入 残差TSM 模块，当用2D 3x3的卷积时，每次插入TSM模块后的时间感受野都会扩大2，故整个框架最后的时间感受野会很大，足以进行复杂的时空建模。

## **实验：**

### **1.准确率比较（与TSN比较）**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **2.参数量效率比较（Something-Something dataset较为复杂）**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **总结思考：**

1.首次从temporal shift 这个视角，通过人为地调度 temporal channel 的顺序让网络学到其交互的时空特征，非常地高效实用。

2.变化顺序可以通过移位操作进行，计算量几乎可以忽略（相比于卷积），硬件非常友好。

3.由于现在channel shift尺度固定, 多尺度multi-scale shifted 是否更有效？

4.TSM模块的迁移泛化性很好，可以嵌入任意的2D CNN达到3D的精度，从而在保证精度的基础上提高效率，对嵌入式开发有帮助。

对于**卷积**操作：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

对于**全连接**操作：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## 参考

[时空建模新文解读：用于高效视频理解的TSM](https://zhuanlan.zhihu.com/p/50798936)