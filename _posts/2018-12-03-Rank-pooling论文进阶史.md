---
layout: post

title: Rank pooling论文进阶史

subtitle:   动作识别论文阅读
date: 2018-12-03
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

- action recogniton
---

## **一 . 起**

[**Modeling Video Evolution For Action Recognition**](http://www.cv-foundation.org/openaccess/content_cvpr_2015/papers/Fernando_Modeling_Video_Evolution_2015_CVPR_paper.pdf)

Basura Fernando, Efstratios Gavves, Jose Oramas, Amir Ghodrati and Tinne Tuytelaars Conference on Computer Vision and Pattern Recognition **CVPR 2015**

![darwin](/../img/2018-12-03-Rank-pooling论文进阶史/darwin.png)本篇论文提出了一种新型的方法，用来描绘视频人体动作识别的时序信息。

假设有一种函数，能够根据视频帧的表观信息进行时序排序，根据不同的视频学习训练这样一个排序机器，从而得到这样的函数。而这个排序机器的参数就作为新的视频表达。

![clipboard](/../img/2018-12-03-Rank-pooling论文进阶史/clipboard.png)

动态图计算过程： 

原始帧为x，则一个视频帧序列为X=[x1,x2,…xn]。 

1、对输入的每一帧，计算它们的特征向量（HOG、HOF、MBH、TRJ） 

2、对特征向量进行smooth，time varying mean vector,mt=1t×Σtτ=1Xτ 

3、然后通过学习RankSVM得到参数u

本文提出了一种基于函数的时序池化方法，能够描绘视频序列数据的隐藏结构。例如，包含时间信息的帧级特征。将展示一个函数的参数被转化作为新型鲁邦的视频表达特征。我们将这些图片帧级特征按时间排序，通过这样的排序机制训练学习池化函数。

发现比普通的平均池化baseline要高7-10%

用ranking function的参数编码视频的帧序列。它使用一个排序函数（ranking function）主要基于这样的假设：帧的appearance的变化与时间相关，如果帧vt+1在vt后面，则定义

![9-2047326598](/../img/2018-12-03-Rank-pooling论文进阶史/9-2047326598.png)

此外，假设同一动作的视频帧序列，学习到的排序函数的参数，应该的大致一致的。

Rank pooling 的方法是使用一个RankSVM的学习排序算法计算的。整个Rank pooling的学习过程可以总结如下：

（1）输入的数据为处理过的帧序列V，由于RankSVM实际上是有监督学习，所以序列的顺序是知道的

（2）如上定义了序列的先后顺序

![4-2024102310](/../img/2018-12-03-Rank-pooling论文进阶史/4-2024102310.png)

定义正例样本为

![2-1995739517](/../img/2018-12-03-Rank-pooling论文进阶史/2-1995739517.png)

其中时间ti在tj之后，反例样本为它的相反数。

（3）可以通过SVM的学习算法，学习如下的凸优化问题 

![68-150556891](/../img/2018-12-03-Rank-pooling论文进阶史/68-150556891.png)

（4）如果学习到的参数为u，则一个vi的score定义为

![71-466214553](/../img/2018-12-03-Rank-pooling论文进阶史/71-466214553.png)

并且有

![1-1005178863](/../img/2018-12-03-Rank-pooling论文进阶史/1-1005178863.png)



## **二 . 承**

[**Dynamic Image Networks for Action Recognition**](http://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Bilen_Dynamic_Image_Networks_CVPR_2016_paper.pdf)

Hakan Bilen, Basura Fernando, Efstratios Gavves, Andrea Vedaldi, Stephen Gould

Conference on Computer Vision and Pattern Recognition CVPR 2016

![clipboard(2)](/../img/2018-12-03-Rank-pooling论文进阶史/clipboard(2).png)

在dynamic论文中发现，这样的参数向量u，事实上与image是同等大小的，也就是说，它本身是一张图片，那么就可以把图片输入到CNN中进行计算了。如下图可以看到一些参数向量u pooling的样例

**本文提出了一种新型的视频的压缩表示（compact representation）：dynamic image**

什么是**dynamic image：**

借鉴rank pooling思想是，学习一个排序机器，将其函数的参数作为新视频动态表征。

将所有视频的动态信息精炼提取到一张图片里面

优点：

1.动态图片能够利用所有使用与图片的CNN架构，同时能拥有推理长程动态视频。

2.动态图片非常有效：获取简洁高效，将分析视频的工作量简化为分析图片

3.表达紧凑。转化为一帧的数据量有助于大数据量的索引

4.泛化性强。可应用在不同序列不同模态（动态RGB，动态Flow）

![clipboard(1)](/../img/2018-12-03-Rank-pooling论文进阶史/clipboard(1).png)

## **三 . 转**

[**Learning End-to-end Video Classification with Rank-Pooling**](http://jmlr.org/proceedings/papers/v48/fernando16.pdf)

Basura Fernando, Stephen Gould International Conference on Machine Learning ICML 2016

![cnnnet](/../img/2018-12-03-Rank-pooling论文进阶史/cnnnet.png)

CNN+temporal pooling layer

时序池化层能将任意长序列视频问题转化为固定长度的向量表示。

## **四 . 合**

[**Discriminative Hierarchical Rank Pooling for Activity Recognition**](http://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Fernando_Discriminative_Hierarchical_Rank_CVPR_2016_paper.pdf)

Basura Fernando, Peter Anderson, Marcus Hutter, Stephen Gould

Conference on Computer Vision and Pattern Recognition **CVPR 2016**

![hrp](/../img/2018-12-03-Rank-pooling论文进阶史/hrp.jpeg)

**分层排序池化：非线性特征函数+rank pooling**

**大容量的动态编码机制**

66.9% HMDB51  91.4%UCF101



### 参考

[Rank pooling for video analysis and human action recognition](http://users.cecs.anu.edu.au/~basura/RP/)