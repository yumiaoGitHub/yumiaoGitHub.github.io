---
layout:     post
title:      MotionNet Hidden Two-Stream Convolutional Networks for Action Recognition
subtitle:   人体动作识别论文阅读
date:       2018-03-08
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition
---

## 摘要

传统optical flow行为识别算法有两个缺点：

1、提前计算 optical flow需要额外占用GPU计算**时间**和存储**空间**，已经成为two-stream算法的瓶颈。

2、传统的optical flow计算方法完全独立于two-stream框架，不是端到端训练，提取的运动信息**不是最优**的。

实现end-to-end也很有挑战：没有optical flow这类带标注的数据集，不能使用在ImageNet等预训练的模型，不能简单使用传统 optical flow estimation损失函数。



## **整体模型**

![clipboard(12)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(12).png)

因为在行为识别中已经**隐含生成的运动信息**，论文称这种模型为hidden （**motion**） two-stream networks。不过需要注意的是，论文中生成的运动信息和传统的光流信息是有区别的。

#### **利用CNN预测光流**

一般的卷积神经网络都被用来进行分类，而去掉全连接层的**全卷积神经网络（FCN）**可以用于对每个像素点进行预测。使网络能从两张连续的图片中预测光流场。

**监督训练——FlowNet**

![clipboard(18)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(18).png)

![clipboard(10)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(10).png)

![clipboard(2)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(2).png)

1.FlowNet以光流标签为ground truth，是有监督训练，数据集小而单一，训练受到限制

2.以EPE（光流预测错误——越小越好）为检测光流预测的评判标准，不能达到最优效果

#### **非监督训练——MotionNet**

![clipboard(5)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(5).png)

论文把光流计算的过程看做是**图像重建**的过程。比如相邻的两帧图片I1和I2，CNN产生光流帧V。在给定V和I2情况下，能够重建I1’帧图像。损失函数的目标是最小化I1’和I1图像的误差。

**根据光流和下一帧图片完美重构当前图片，那么可以认为当前网络有效表达的隐藏动作**

### **损失函数：**

#### 1、标准的像素级重建误差函数

![clipboard(17)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(17).png)

光流实现的假设前提：

- 相邻帧之间的亮度恒定。

- 相邻视频帧的取帧时间连续，或者相邻帧之间物体的运动比较“微小”。

- 保持空间一致性。即同一子图像的像素点具有相同的运动。

![clipboard(7)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(7).png)

光流直观理解是：第t帧的时候A点的位置是(x1, y1)，那么我们在第t+1帧的时候再找到A点，假如它的位置是(x2,y2)，那么我们就可以确定A点的运动了：

(Vx, Vy) = (x2, y2) - (x1,y1)

I1图片中A点坐标为(i , j)   I2图片中A坐标为（i+Vx , j+Vy）

根据亮度不变性， *I*1- *I2=0*

![clipboard(11)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(11).png)

#### 2、平滑损失函数

解决光圈问题导致的运动信息的模糊性。

![clipboard(6)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(6).png)

#### 3、结构相似性（SSIM）损失函数

可以帮助模型学习框架结构，清晰轮廓边界的光流场。

![clipboard(16)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(16).png)

![clipboard(3)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(3).png)

![clipboard(9)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(9).png)

### **CNN架构选择：**

![clipboard(8)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(13).png)

### **模型融合：**

- #### **Stacked Temporal Stream**

![clipboard(15)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(15).png)

在MotionNet直接加入到temporal stream CNN之前，论文采取一些措施：

1、规范化计算的光流信息。

2、微调和损失函数选择。论文通过对比采用MotionNet 和temporal stream CNN都进行微调，所有的损失函数都需要计算。

3、输入图像采用11帧叠加方式，生成**10**帧的光流信息（效果最佳！）

- #### **Branched Temporal Stream**

![clipboard(4)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(4).png)

使用共享参数的方式：MotionNet的encoder过程和Temporal Stream CNN使用相同的参数。训练时，MotionNet先预训练，然后再对时间流调优

![clipboard(14)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(14).png)

虽然Branched结构能够提升效率但是没有发挥出与空间流互补的优势！可能是因为破坏了双流法中特殊的结构——将时间和空间分离单独计算表观特征（Appearance）和动态信息（Motion）

## **实验分析：**

![clipboard(8)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(1).png)

速度快了10倍！

将时间网络换成VGG16或者TSN：

![clipboard(8)](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard(8).png)



## **总结反思：**

**优点：**

1、模型不需要任何附加标签信息，因此没有前期资源需求。

2、模型计算高效，比传统计算方法快10倍。

3、没有额外存储空间需求。

4、使用CNN计算光流有更多的改进空间。

**局限性：**

MotionNet不遵守亮度不变的假设，因此处理高饱和或者动态纹理时存在困难（可能出现假影），例如：水面、天空、镜子等。

**改进方向：**

1.smoothness loss对提升性能有所帮助，探索其他方式例如edge aware loss

2.采取更为有效的融合方法来改善目前单一的late fusion

3.解决错误标签问题

4.去除全局相机移动和局部遮挡将对光流预测和行为识别大有好处



**附录：整体模型参数**

![clipboard](/../img/2018-03-08-MotionNet-Hidden-Two-Stream-Convolutional-Networks-for-Action-Recognition/clipboard.png)