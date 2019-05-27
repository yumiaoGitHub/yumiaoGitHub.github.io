---
layout:     post
title:     MOJITALK: Generating Emotional Responses at Scale
subtitle:   神奇的表情
date:       2018-08-18
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - 机器学习
---

**MOJITALK: Generating Emotional Responses at Scale**

![img](https://note.youdao.com/yws/public/resource/ad01b691ccf2fd643c7b906c8e30a14f/xmlnote/CBD4978AAAE348E681EF52C94932FE01/15255)

## **摘要**

本文是清华大学和 UCSB 发表于 ACL 2018 的工作，**论文旨在教会机器生成有情绪的回答**，比如当用户伤心的时候，机器回答一定不能很开心。**这项工作的难点在于缺少大规模标注好的情感训练集，以及如何控制生成回答的情感**。现有的情感数据集对深度模型都太小，并且只有有限的几个分类（生气、开心，或者正面、负面）。 

**本文解决方案如下：**

1. 使用含有 emoji（选择了 64 种）的 Twitter 数据来做自动情感标注（规模：600K） 

2. 在生成回答时，根据给定的 emoji 来生成不同情感的回答

![img](https://note.youdao.com/yws/public/resource/ad01b691ccf2fd643c7b906c8e30a14f/xmlnote/35FB7A10683343F7B323C96A9C7FBC23/15261)

## **模型**

本篇论文的目的是生成带有emoji标签的情绪化回复。因此主要运用生成模型

**1.基础版：基于注意力的序列生成模型**

（Attention-Based Sequence-to-Sequence Model）

**2.条件变分自动编码器Conditional Variational Autoencoder(CVAE)**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**3.加强版CAVE**

- **根据emoji标签的位置调整奖励**

当emoji在所有表情包中排名越靠前，我们假设这个模型在表达情绪方面越成功。因此对于应答不需要过多的调整。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

- **同时学习情绪的精确度和表达的合适度**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **实验分析**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **总结思考**

1.论文巧妙的利用大众聊天中（Twitter）表情包进行情感标注，弥补了当前标注数据集稀缺的问题。（数据是非常珍贵的资源）

2.通过不同模型的研究与优化（增强CAVE），结合人工和自动反馈优化模型

3.在chatbot有很好的应用场景，情感化回复应答等等。