---
layout:     post
title:      Coarse-to-Fine Asynchronous Fusion
subtitle:   人体动作识别需求分析综述
date:       2018-02-02
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition
---


![img](https://note.youdao.com/yws/public/resource/2790a0fca37f9722f01881d7774fd0b7/xmlnote/8B2DF2200B674893B7EFF74A2A99A438/8517)

## **摘要：Coarse-to-Fine + Asynchronous Fusion**

- **由粗到细的分析网络**



![img](https://note.youdao.com/yws/public/resource/2790a0fca37f9722f01881d7774fd0b7/xmlnote/69E44872CDB741E78EB6C87253E6E785/8520)

- **异步融合**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **模型框架**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)





### **Coarse-to-Fine Network**

#### **1. 多粒度特征获取**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

#### **2. 调整类别分组**

通过CNN_M_2048更小的卷积网络实现在不同粒度下分类组：top 5, top 3, and top 1

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

#### **3. 粗到细网络结果综合**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

损失为多粒度特征获取损失+由粗到细模块结合损失

### **Asynchronous Fusion Network**



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

逐流特征融合（Stream-Wise）

1\*1卷积ConvNet

异步综合（Asynchronous integration）

LSTM——最终输入预测结果

## **实验结果**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **总结**

- 网络结构较为复杂，多处用了convnet、LSTM，计算量比较大。
- 通过不同卷积层的输出结果，分析由粗到细的行为识别判断（beam search+class group）值得借鉴。
- 融合的方式不再是简单的最终分类加权平均，1\*1streamwise的融合方式+LSTM逐层递进的推断可以参考