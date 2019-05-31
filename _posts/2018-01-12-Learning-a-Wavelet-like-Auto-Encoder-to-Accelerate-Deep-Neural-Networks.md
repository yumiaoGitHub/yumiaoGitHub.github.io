---
layout:     post
title:      Learning a Wavelet-like Auto-Encoder to Accelerate Deep Neural Networks
subtitle:   人体动作识别论文阅读
date:       2018-01-12
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- 小波变换
    - action recognition
---

## **摘要**

下采样的方法达到网络加速，但是下采样会导致的信息损失而降低网络性能，WAE既能**降低图像分辨率，又不损失信息**，保持分类准确率。WAE借助小波分解思想，将原图分解为两个低分辨率图像：一个携带高频信息（图像细节信息或者噪声），一个携带低频信息（图像全局主要信息）

## **网络框架**

![clipboard(10)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(10).png)

![clipboard(2)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(13).png)

### **Encoding layer**

输入图片通过编码层（分解层）得到IH（高频图）和Il（低频图）

具体过程：

输入图片经过3层3\*3卷积核（可以比拟为7*7的卷积核的感受野）后，以步长为2作用3\*3卷积进行下采样得到分辨率为原始图片1/2的高频图和低频图，通过增加一个L2范数的约束。对图像像素值的大小进行限制，像素值小的图像就称之为高频信息图。

![clipboard(7)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(7).png)

![clipboard(19)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(19).png)

![clipboard(22)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(22).png)

### **2.Classification**

![clipboard(17)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(17).png)

IH和IL分别经过一个VGG的卷积部分，得出特征FH,FL； 

右半部分 分为上下，先说上边，FH和FL进行“融合”，也就是按通道concat，然后输入FC层，得出一个1000维的向量 V1； 

下边，FL直接输入到FC层得到向量V2； 

最终的网络分类输入是 V1+V2(element-wise) 

### **3.Decoding layer**

通过IL和IH恢复原图像作为训练损失函数的比较

![clipboard(21)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(21).png)

![clipboard(14)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(14).png)

### **目标函数**

训练分为三个阶段:

Stage 1: WAE training

Stage 2: Classification network training

Stage 3: Joint fine tuning

整体训练Loss[3] = 分类Loss[1] + WAE Loss[2]

![clipboard(5)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(5).png)

#### **分类Loss**

![clipboard(23)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(23).png)

![clipboard(20)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(20).png)

#### WAE Loss

![clipboard(18)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(18).png)

 Auto-encoder（WAE）主要是看重构误差

![clipboard(9)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(9).png)

要获得“高频图”增加的一个约束，用以约束其中一个特征图的像素值的大小： 

![clipboard(7)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(7).png)

## **计算量分析**

对于CNN，所有卷积层的总计算复杂度为：

![clipboard(11)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(11).png)

d：卷积层数

n：通道数

s：卷积核尺寸

m：输出特征图尺寸

输入高频图和低频图为1/2大小，m'=m/2

融合网络中IH通过VGG时通道数n为原来的1/4

![clipboard(12)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(12).png)

## **实验分析**

**Wavelet+CNN**

![clipboard(15)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(15).png)

**离散小波变换（DWT：9/7实现）**

将图片分解为四个分辨率减半的通道：cA（低频信息近似输入图片，IL）, cH, cV, cD（高频细节IH）分别送入分类器

由于使用了更有效率的DWT进行图片分解，速度比WAE快。但是由于直接将图片的高低频信息分离（WAE通过IH能量最小化将信息集中到IL）降低了识别率

**Decomposition+CNN**

与WAE思路相同，分解为两个分辨率减半的图片，但是在分解通道上没有L2约束，只用分类损失训练

![clipboard(2)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(2).png)

（a）为输入图片

（b）（c）是使用WAE分离得到的低频图和高频图(互补信息)

（d）（e）是使用Decomposition+CNN分离得到的低频图和高频图（差不多）

**证明使用L2约束最小化IH能量的有效性**

**分离通道的分析**

![clipboard(4)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(4).png)

**总体结果**

![clipboard(8)](/../img/2018-01-12-Learning-a-Wavelet-like-Auto-Encoder-to-Accelerate-Deep-Neural-Networks/clipboard(8).png)

### 参考

[小波变换与应用PPT](<https://note.youdao.com/ynoteshare1/index.html?id=1a567c54579b323b84a012ad959bdbfd&type=note>)

