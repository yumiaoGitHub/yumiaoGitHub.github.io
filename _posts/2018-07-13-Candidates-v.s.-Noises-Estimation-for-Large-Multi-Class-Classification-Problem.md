---
layout:     post
title:     Candidates v.s. Noises Estimation for Large Multi-Class Classification Problem
subtitle:   多分类问题
date:       2018-07-13
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - NLP
---

## **摘要**

这篇论文是腾讯AI Lab主任 张潼在2018 ICML发表的论文，主要讨论了大型多分类问题。图像分类和语言建模等很多任务的类别数量往往很大，**采样**是应对这类任务的常用方法，能够帮助降低计算成本和提升训练速度。这篇论文对这一思想进行了扩展，提出了一种使用一个类别子集（**候选项类别，其余类别被称为噪声类别——会被采样用于表示所有噪声**）的用于多类分类问题的方法：候选项与噪声估计（CANE）。研究者表明 CANE 总是能保持一致的表现并且很有计算效率。此外，当被观察到的标签有很高的概率属于被选择的候选项时，所得到的估计器会有很低的统计方差，接近最大似然估计器的统计方差。

## **整体结构**

多分类问题的常用解决方法有1.采样 2.树结构。

在语言模型中，Noise-Contrastive Estimation（NCE）采样方法最早用于多种语言建模，建立概率密度（word embedding例如词嵌入、文本生成和机器翻译）。NCE将多分类问题转化为二分类问题——目标类和噪声类。负采样（Negative Sampling）就是NCE的一个简化方法。为了区别于噪声类，作者综合前面研究者的方法提出了**“候选类”（candidate classes）**这种方法可以有效提升统计效率。

另一种解决方法是树结构，每一种类被定义为一个叶节点。树里面的每一个非叶节点可以看做是局部分类器，因此多分类问题被分解我多个二分类问题，有效的加快了训练速度但是在准确率上有一定的损失。其准确率极大的依赖于数结构的质量，常用的有Filter Tree、Hierarchical Softmax (HSM)、LOMTree。作者的研究与树结构的方法互补，可以应用于候选集的选择，这篇文章没有详细展开，**可以作为后来研究者的方向。**

**整体方法：**



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**束树算法：**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **实验分析**

**自然语言建模应用：**

一般情况下给定文本h中目标单词w的概率分布为

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

而采用本文CANE方法，则由w单词本身和其候选集、噪声集共同表示

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

实验数据集选择如下：

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**可以看出，随着分类类比K的上升，CANE表现超过了softmax并且训练效率高**



## **总结反思**

本文提出的CANE方法能够快速学习大型多分类问题，并可应用于自然语言建模当中。相比于传统的softmax分析，本方法拥有更好的计算效率。

本篇论文多数学推导，有些细节不是很理解，但是这些思路都是我们可以研究的方向。智能机器人是多媒体垂直领域发展的代表，会涉及到图像理解并用NLP表达的。