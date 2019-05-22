---
layout: post

title: What Makes a Video a Video：Analyzing Temporal Information in Video Understanding Models and Datasets

subtitle:   动作识别论文阅读
date: 2018-11-16
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:
- action recogniton
---

![img](https://note.youdao.com/yws/public/resource/ac048d398a083a896e026af0f2daaaa7/xmlnote/D12C69C9A7BB4121AA2B529BFFE50360/19468)

## **摘要：**

**用量化的方法来分析motion对于视频理解的作用有多大。**

这篇论文第一次以量化的形式展示了具体有多少类的视频并不需要motion信息就可以很好地完成分类，我觉得比较有意义。比如在UCF101数据集中，最不需要motion（即只使用空间信息就足以分类视频）的类是“WalkingWithDog”，最需要motion来帮助分类的类是“PushUps”，Kinetics中的这两个类别分别是“Playing Paintball”和“JuggleBall”，看起来还是挺合理的。

40%的UCF101视频和35%的Kinetics视频不需要motion信息就能达到与使用motion信息情况类似的性能。对于有些类，motion信息作用很大，而且需要更多的motion信息来进一步提高分类准确率。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

## **模型：**

（1）改变了视频帧的时间上分布（temporal distribution），因为训练时使用的是16帧的clip，而测试采用采样过的帧构成的clip，训练和测试数据的分布不一致。

（2）可能将视频中最重要的帧，对视频分类最有用的帧给丢掉了。

### **类别无关的时域生成器**

**GAN在视频理解中的应用，用下采样的帧序列来生成完整的帧序列。**

将输入视频进行下采样，实验中将16帧的clip分别采样到1，2，4，8帧这四种情况，然后将选出来的帧输入到由cycleGAN构成的时域生成器中，生成16帧的clip，通过计算生成的clip和原先的clip输入到C3D网络中得到的不同层的feature map之间的归一化的L2距离作为loss（即Perceptual Loss，感知损失）进行网络优化。整个过程是无监督的，视频的类别标签和监督损失是没有使用的，因此是类别无关的。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

Temporal Generator：基于[cycleGAN](https://blog.csdn.net/qq_21190081/article/details/78807931)生成帧，构成视频输入训练好的网络

[perceptual loss](https://www.jianshu.com/p/b728752a70e9)：高级特征内容+风格损失，而不是逐像素对比（图像风格转换问题）



## **给出了运动不变的关键帧选择器**

运动信息无关：不管运动信息怎么变化，得到的关键帧每次都一样。作者提出了两种关键帧选择的方法。

第一种是给定候选帧和一个固定的响应函数，选择响应函数的值最大（即置信度最高）的那个候选帧作为关键帧，这种选择方式作者称为**Max Response**。

第二种是选择所有正确分类的候选帧作为关键帧（即只从候选帧中删去错误分类的帧），这种方式叫做**Oracle**。这种方式作者也说有些”cheating“，因为将ground truth利用了起来，而实际测试的时候ground truth是不可知的。不过这种方式也是运动不变的，没有用到额外的运动信息。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



虽然这篇论文没有在数据集上有相比最好结果的提升，但是通过分析时域信息的作用，说明现有方法还是没有很好地用到motion的信息，或者说motion的作用还没有完全地利用起来，可能需要大家设计针对视频的网络结构来把motion更好地利用起来。



## **实验：**

本文的方法在采用1帧的情况下，比原先的16帧在UCF101上只差了6个点（73% v.s. 79%），而在Kinetics上则只差了5个点（42% v.s. 47%）。所以motion额外提供的信息其实作用是比较小的。

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



## **反思：**

1. 第一次以量化的形式展示了哪些类的视频不需要motion信息就可以地完成分类。
2. 通过分析时域信息的作用，说明现有方法还是没有很好地用到motion的信息，或者说motion的作用还没有完全地利用起来，可能需要大家设计针对视频的网络结构来把motion更好地利用起来。
3. 视频理解中的关键帧选择能够在未来有大的突破。

**可以尝试使用增强学习的方式训练一个关键帧选择器**

**这篇论文的总体也可以看做是GAN在视频理解中的应用，用下采样的帧序列来生成完整的帧序列。**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)