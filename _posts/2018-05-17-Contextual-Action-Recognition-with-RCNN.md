---
layout:     post
title:      Contextual Action Recognition with R*CNN
subtitle:   人体动作识别论文阅读
date:       2018-05-17
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - action recognition
---

## **摘要**

基于动态图像的行为识别主要是学习到图片中人体的动作，而真正识别一种行为需要多方面的线索相结合：本文很好的结合了运动物体和环境因素来实现静态图像的行为识别。作者将R-CNN改造成能够对图片进行分类同时回归物体位置的网络，称为R∗CNN，在PASAL VOC Action dataset上的map达到了90.2%，这是目前最高的（2015年ICCV）。

## 模型分析

作者使用R∗CNN来提取**包含人**的首要区域（primary）和**包含物体、环境因素**的次要区域（secondary）

选取首要区域很简单，问题是如何定义包含环境因素的次要区域呢？

使用**Fast R-CNN**的方法来对每张图片生成多个region，整体框架如下图：

红色的是选出的首要区域。绿色的是多个次要区域，可以看到次要区域选出了很多个，但是最后和首要区域的score相加时用了一个max，就只选取了一个次要区域。

![clipboard(6)](/../img/2018-05-17-Contextual-Action-Recognition-with-RCNN/clipboard(6).png)

 I代表一张image图片，r代表I中的一个region区域，某个动作类别α， 

![clipboard(4)](/../img/2018-05-17-Contextual-Action-Recognition-with-RCNN/clipboard(4).png)

得到某个动作α的分数以后，就可以用类似softmax的函数来计算概率：

![clipboard(1)](/../img/2018-05-17-Contextual-Action-Recognition-with-RCNN/clipboard(1).png)

## **实验分析**

最后的实验结果在数据库上选出的首要次要区域示意图如下

 **1.PASCAL VOC Action**

![clipboard](/../img/2018-05-17-Contextual-Action-Recognition-with-RCNN/clipboard.png)

**2. MPII Human Pose Dataset**

![clipboard(3)](/../img/2018-05-17-Contextual-Action-Recognition-with-RCNN/clipboard(3).png)

**3. Attribute Classification**

![clipboard(5)](/../img/2018-05-17-Contextual-Action-Recognition-with-RCNN/clipboard(5).png)

## **总结拓展**

- 本文提出了一种简单但有效的方法——R*CNN：**主要区域（人体）**、**次要区域（物体场景）**的预测结果加权平均。综合利用人体动作和物体场景信息进行人体动作识别，参考了上下文线索（context information clue）
- 由于次要区域经过Max操作与主要区域加权平均，每次预测只有一个场景或者物体信息得到参考，不具有全局性，存在上下文**线索信息缺失**。

  

























![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)