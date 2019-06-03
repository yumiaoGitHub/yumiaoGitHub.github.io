---
layout: post

title: Compositional Learning for HOI（ECCV 2018）

subtitle:   动作识别论文阅读
date: 2018-11-02
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- action recogniton
---

![clipboard(3)](/../img/2018-11-02-Compositional-Learning-for-HOI（ECCV-2018）/clipboard(3).png)

## **摘要**

本文是EECV 2018解决**人-物关联**问题（HOI human-object interactions）

由于日常生活的动作丰富且复杂，需要大量数据，且对动作的表征不能对新类别有效泛化。因此开始探索利用**图卷积**进行**零样本**学习解决HOI问题。创新的提出了**组合学习**的概念，将动名词分类器进行组合迁移，应用在图像和视频上。

action=motion(verb)+objects(noun)

![clipboard(4)](/../img/2018-11-02-Compositional-Learning-for-HOI（ECCV-2018）/clipboard(4).png)

## **模型：**

![clipboard](/../img/2018-11-02-Compositional-Learning-for-HOI（ECCV-2018）/clipboard.png)

(a)编码SVO（主谓宾）的图，每个动词和名词，以词向量作为节点的特征（方形）与动名词相连接的原形节点表示action node动作节点。在动词和动词之间，名词与名词之间也可以建立联系（WordNet）

(b)图卷积操作：传播特征，给空白的action节点新的表示特征，将会与视觉特征相融合

(c)学习动作内容和视觉输入的相似度矩阵

得分函数为：

![clipboard(6)](/../img/2018-11-02-Compositional-Learning-for-HOI（ECCV-2018）/clipboard(6).png)

x：图像视觉特征； yv：动作标签；yn：名词标签；K：图结构

### **1.图的构建：（基于人的先验知识）**

- 每个节点表示为Vv和Vn，用词向量Zv、Zn表示节点特征
- 每个在SVO里面每个动作-物体对定义了一种人和物体的联系Va,初始化为0，通过学习之后获得特征Za.
- 每个动词节点必须通过有效的action节点与名词相连接，对应的，每种交互就会在图上增加一条路径
- 名词和动词之间也会通过WordNet彼此连接

### **2.图卷积应用于组合学习**

![clipboard(2)](/../img/2018-11-02-Compositional-Learning-for-HOI（ECCV-2018）/clipboard(2).png)

通过K层GCN层，模型构建了非线性转换是的节点在构建动作表示时能考虑K近邻

### **3.图应用于零样本识别**

![clipboard(1)](/../img/2018-11-02-Compositional-Learning-for-HOI（ECCV-2018）/clipboard(1).png)

![clipboard(5)](/../img/2018-11-02-Compositional-Learning-for-HOI（ECCV-2018）/clipboard(5).png)