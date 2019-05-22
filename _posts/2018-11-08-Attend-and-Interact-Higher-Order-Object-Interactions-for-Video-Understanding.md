---
layout: post

title: Attend and Interact Higher-Order Object Interactions for Video Understanding

subtitle:   动作识别论文阅读
date: 2018-11-08
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- action recogniton
---

## **摘要：**

人体动作通常与场景中的物体有着复杂联系。然而现存的方法进行细粒度的视频理解，或者视觉关系检测通常依赖单一物体或者成对物体间的联系。而且对于视频，上百帧的计算占用大量空间和时间。因此，本文提出一种有效进行细粒度视频理解的方法，实现在任意子分组的物体间实现**高阶**交互理解。在动作识别领域（Kinetics）同样有效并且比传统的成对关系建模计算快3倍。

![img](https://note.youdao.com/yws/public/resource/1f6e9c35143809586c20dcdf6d617692/xmlnote/A546C01F9F3D4CC08B83174383E52347/19155)

- **组内**交互理解：用相同颜色r, g, b的ROI框标注，表明对象间存在联系
- **组间**交互理解：参与高阶交互对象的推理（不同颜色的ROI联系）



## **模型：**

### **1.粗粒度图像特征**

视频中使用LSTM来结合图像序列的效果并不好，因为相邻帧图像特征相近，而缺乏时间变化。那么抛弃RNN，encoder过程就没有隐藏节点，self-attention就无法进行？——一下子把序列的全部内容输进去，采用压缩点乘注意力。

将每个特征转化为词向量，embedding 代替 hidden state 来做 self-attention 了。查询每个词与其他词的匹配程度。最后通过平均池化得到视频表征Vc

**压缩点乘注意力 scaled dot-product attention （**[**SDP-Attention**](https://www.jiqizhixin.com/articles/2018-06-11-16)**）**

**每个物体注意力权重是所有物体组合计算得到，因此能考虑物体间内在关联**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

更快、更节省空间，并且更容易优化

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### **2.细粒度高阶物体交互**

传统成对物体交互只考虑某一物体和另一个物体的关联。而我们建模分析任意子组间的物体内在联系。每个分组内的成员组成通过可学习的注意力机制决定。二元组和三元组是个特例，表明注意力只集中于某一个物体。

物体检测基础结构：Deformable R-FCN (pre-trained on MS-COCO) with ResNet-101。提取TOP30物体

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**将物体通过一个****区域框****在图像中表示。**没有直接将物体类别信息输入由于存在**跨域问题**。而且可能有物体没有被检测出来；跨时间将物体建立联系计算量很大，因此将可变长的物体组存储在跨时间的高维空间中。

加入物体间的交互最简单方法是直接相加 或者采用主宾构成结构对。但对于视频来说数据量太大且不包含时间推理而不适用。

**Recurrent Higher-Order Interaction (HOI):**

**动态选择对于区分人体动作很重要的物体候选框**

**HOI：**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**Recurrent HOI:**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

最后将粗粒度图像特征和细粒度高阶物体特征送入最后一层全连接层：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



**实验分析：**

**1.Does object interaction help?**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**2.Does attentive selection help?**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **总结思考：**

1.**新颖性**：将图像特征和物体特征转化为词向量，进行拼接理解，适用于行为类别的拓展。zero-shot问题更好泛化。

2.**注意力处理**：深入研究物体交互，自动选择有价值的物体信息

3.**改进之处**：物体交互只是区域框的交互理解，转化为轮廓特征更好；融合物体类别信息（one-hot编码？）