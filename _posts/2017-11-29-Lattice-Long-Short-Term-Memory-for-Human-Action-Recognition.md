---
layout:     post
title: Lattice Long Short-Term Memory for Human Action Recognition
subtitle:   人体动作识别论文阅读
date:       2017-11-29
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- LSTM
---

## 摘要

以卷积方式简单地将RNN应用于视频序列隐含地**假定视频中的运动在不同的空间位置上是静止的**。 这个假设对于短期运动是有效的，但是当运动持续时间很长时是无效的。LRCN解决了不同类型的输入问题，然而采用**矩阵乘法**的传统LSTM没有利用视频帧中的空间相关性。 直接将LSTM模型应用于基于视频的动作识别是不令人满意的，因为帧之间的空间相关性和运动动态不能很好地呈现。Lattice-LSTM (晶格LSTM),它通过学习单个空间位置的存储单元的独立隐藏状态转换来扩展LSTM。

### **提出问题——高维数据得到映射函数难！**

$$
H_{t} = W_{H}H_{t-1}+W_{X}X_{t}
$$



![clipboard(3)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(3).png)

![clipboard(12)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(12).png)

将公式（3）简化为：

![clipboard(6)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(6).png)

X表示视频帧序列，维度很高，f是一个在LSTM中需要学习获得的状态转移线性函数，由于其维度太高，一般很难通过学习得到精准的映射函数。在机器学习中，一个提升模型容量的经典方法是将**特征空间分区到局部小区域（local cell）**，然后在每个局部区域中学习单独的映射关系。

**将特征空间分区为局部区域：将高维空间映射函数学习转化为每个单独的像素映射学习**

ps:模型的容量是指其拟合函数的能力（Model capacity is ability to fit variety of functions），低容量underfit，高容量overfit（记忆了训练集的太多性质在测试集上表现不好，例如用一次项，平方项，九次项来拟合一个二次函数）一个提升容量的方法就是选择假设空间（线性——指数）

![clipboard(7)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(7).png)

### **解决问题——Lattice LSTM**

**local superposition operation + multi-modal learning**

- #### **局部空间拟合叠加**

![clipboard(4)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(4).png)

在t时刻由Lattice LSTM新生成的细胞记忆：

![clipboard(8)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(8).png)

前半部分是正常的输入与权重卷积，后半部分表示局部叠加和

![clipboard(10)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(10).png)

（i; j）表示内核中的位置，m和n索引位置，l和k表示输入和输出特征图。 

这种局部滤波操作是在**不同的空间位置用不同的滤波器（类似于分段函数拟合）**。

将这些独立初始化的局部滤波器应用在每个存储单元像素上，以便学习不同像素处的不同时序模式。**计算量不变的情况下，拟合情况（模型容量）有很大改善。**

原先整体：196\*512  局部晶格：（196\*512/9）*9

![clipboard(13)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(13).png)

 根据公式（3）我们只将局部叠加应用于细胞隐藏单元的转换，其他线性组合是卷积的。 因此，Whc的容量比Wxc大。 所以局部叠加将增强存储单元中保存动态信息的能力并且建模解决长期视频中的空间非均匀性问题。

在存储细胞上应用局部叠加操作形成晶格循环存储单元，增强了LSTM学习**复杂运动模式**和解决**长期依赖问题**的能力。

- #### **多模态学习**

控制记忆细胞的输入输出门的权重矩阵使用多模态学习过程：

![clipboard(5)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(5).png)

**将视频RGB帧和相应的flow光流同时馈入系统以学习双流结构的共享输入门和遗忘门。**

![clipboard(11)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(11).png)

Q是损失函数，n是学习率

![clipboard(2)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(2).png)

## **长期短期抽样（Long Short Term Sampling）**

![clipboard(1)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(1).png)

![wpse5a8.tmp](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/wpse5a8.tmp.png)

## **实验**

**n=8个视频短片，每个短片由5个视频帧组成，每个视频短片的采样步长随机。**

每一个视频短片Vclip之间的时间间隔为Ss，其内部视频帧的采样步长为St

“短期”剪辑，覆盖连续帧（st = 1和ss是最小步幅），运动是**平稳**的;

“长期”剪辑覆盖整个视频（st = ss和ss = smax），运动**变化**很大。 

 与传统采样方法相比，采用固定步长和随机提取的剪辑，我们看到大约**2％**的增益。

![wps39a4.tmp](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/wps39a4.tmp.png)

![clipboard(9)](/../img/2017-11-29-Lattice-Long-Short-Term-Memory-for-Human-Action-Recognition/clipboard(9).png)

## **总结**

1. 为了视频中解决长程和复杂动作识别问题，将特征空间划分为局部小区的叠加（Lattice LSTM）
2. 多模态学习：综合利用RGB帧和对于的flow光流帧信息，进行双通道模式学习，共同训练两个输入门和忘记门，而不是将两个流视为分离的实体，从而更好控制输入输出。
3. 采用全新的采样策略，不同的步长采用使得LSTM能更加自由获取到视频片段的信息