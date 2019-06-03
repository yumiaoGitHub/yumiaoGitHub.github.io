layout:     post
title:      End-to-End Learning of Motion Representation for Video Understanding
subtitle:   人体动作识别论文阅读-TVNet
date:       2018-04-20
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition

## **摘要**

本文（**面向视频理解的端到端动作表示学习）**由AI Lab主导完成，并入选Spotlight文章。尽管端到端的特征学习已经取得了重要的进展，但是人工设计的光流特征仍然被广泛用于各类视频分析任务中。为了弥补这个不足，作者创造性地提出了一个能从数据中学习出类似光流特征并且能进行端到端训练的神经网络：TVNet。

## **模型分析**

**将迭代展开为神经网络叠加层**

当前，TV-L1方法通过优化方法来求解光流，是最常用的方法之一。作者发现，把TV-L1的每一步迭代通过特定设计翻译成神经网络的某一层，就能得到TVNet的初始版本。因此，TVNet能无需训练就能被直接使用。更重要的是，TVNet能被嫁接到任何分类神经网络来构建从数据端到任务端的统一结构，从而避免了传统多阶段方法中需要预计算、预存储光流的需要。

![clipboard(8)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(8).png)

### **TVL1 VS TVNet**

最基本光流公式：

![clipboard(12)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(12).png)

Total Variation+L1 norm

![clipboard(17)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(17).png)

分别代表**灰度一致性**和**光滑度条件**条件

线性化**Linearization：**利用[**一阶泰勒展开**](http://en.wikipedia.org/wiki/Taylor_series)来建立图像gradient和displacement之间的关系

![clipboard(4)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(4).png)

建立在两个**假设**基础上：

**第一**，图像变化是连续的——**Gaussian Smoothing**使其变化较为平缓

**第二**，位移不是很大——**Coarse-To-Fine**图像降分辨率建立金字塔

![clipboard(9)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(9).png)

为了遵循凸逼近，引入额外变量V~U

![clipboard(21)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(21).png)

（1）如果v固定

![clipboard(13)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(13).png)

![clipboard(7)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(7).png)

![clipboard(1)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(1).png)

（2）如果u固定

![clipboard(6)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(6).png)

![clipboard(10)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(10).png)

**如此往复迭代得到U和P**

TVL1算法流程如下：

![clipboard(18)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(18).png)

![clipboard(14)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(14).png)

- Gradient-1.（亮度梯度）—— Cov_1:  I1\*Wc [0.5, 0,0.5]

![clipboard(19)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(19).png)

- Gradient-2.（光流梯度）—— Cov_2: Ud\*Wf [-1,1]

![clipboard(15)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(15).png)

- Divergence（散 度）—— Cov_3: Pd1\*W + Pd2*WT[-1,1]

![clipboard(20)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(20).png)

## **实验分析——Going beyond TVL1**

TVNet的某些参数（初始光流向量**U****0**和卷积滤波器的值**W）**是可以被通过端到端训练来进一步优化，这有助于TVNet学习出更丰富以及与任务更相关的特征而不仅仅是光流。

- **光流准确率分析——EPE误差**

![clipboard(16)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(16).png)

- **光流速度分析——FPS**

![clipboard(3)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(3).png)

![clipboard(11)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(11).png)

由于TVNet是在TensorFlow上实现，可以通过增大batch size 方法并行训练。如果将batch = 10，FPS=60

- ### **多任务训练——视频行为识别**

在两个动作识别的标准数据集HMDB51和UCF101上，该方法取得了比同类方法更好的分类结果。与TV-L1相比，TVNet在节省光流提取时间和存储空间的基础上，明显提高了识别精度。

![clipboard(2)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(2).png)

![clipboard(16)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(16).png)

![clipboard](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard.png)

![clipboard(5)](/../img/2018-04-20-End-to-End-Learning-of-Motion-Representation-for-Video-Understanding/clipboard(5).png)

## **总结思考**

1. 本文通过将TV-L1光流法的迭代展开为神经网络层叠加——TVNet实现端到端训练
2. TV-L1架构初始化可以不用训练直接使用，除了得到光流结果之外，也可以通过调优应用在其它任务的网络中，例如动作识别
3. TVNet比其它利用神经网络提取光流方法取得了更高的准确率（FlowNet2.0），并且达到了更快的速度（60FPS）
4. 卷积层代替散度和梯度的思想值得借鉴，完美的借鉴了经典方法

### 参考

[图像处理中的全局优化技术](https://blog.csdn.net/MulinB/article/details/12005723)