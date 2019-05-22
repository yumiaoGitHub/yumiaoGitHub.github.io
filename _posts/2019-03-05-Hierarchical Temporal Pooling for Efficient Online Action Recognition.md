---
layout: post

title: Hierarchical Temporal Pooling for Efficient Online Action Recognition

subtitle:   动作识别论文阅读
date: 2019-03-05
author: Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

- action recogniton
---

![img](https://note.youdao.com/yws/public/resource/42667a1e2b11dee4c7eac007437e831b/xmlnote/C2453570BE2142F0ABD5F10074BCF77F/21654)



## **摘要：**

本文主要解决**高效率在线动作识别**问题。

ECO（高效视频理解）的思想是：**2D卷积**提取空间表观特征，**3D卷积**提取时间动态特征。本文借鉴了ECO混合2D和3D的思想，提出用**2D卷积**提取空间表观特征，**3D池化**提取时间动态特征。创新的提出了**分层时序池化模块HTP**（Hierarchical  Temporal pooling），类似TSM网络构建了HTP-Net。由于只使用了3D池化，相比于卷积计算量很小。HTP-Net(RGB)达到了42vps(*16batch_size=672fps)

## **模型：**

### **HTP模块**

参照TSN采样策略，将视频分为等长N段，再随机从某一段中抽取一帧图片，构成N帧图片输入2D模块（蓝色方块）生成空间表观特征，接着输入到3DHTP模块（绿色方块）来连续的融合时序动态信息和空间表观信息。由于每个HTP模块的时序下采样是采用3D池化操作，因此没有任何额外的参数从而模型也很小。

![img](https://note.youdao.com/yws/public/resource/42667a1e2b11dee4c7eac007437e831b/xmlnote/D07BF6F46FEA456292D0CE0743847A6B/21656)



![img](https://note.youdao.com/yws/public/resource/42667a1e2b11dee4c7eac007437e831b/xmlnote/9A4594DED8854452B62CFA9F942FE320/22107)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### **维度排列调整**

2D conv处理（C\*W\*H）tensor。表明视频帧间的时序联系是被忽略的。

3D conv处理（C\*T\*W\*H）tensor。包含了时间域T

**时序堆叠：(N\*C\*W\*H) ——>(C\*N\*W\*H)**  N帧图片采样相当于这里的T

```
=========== 3D network=======

layer {

name: "r2Dto3D"

  type: "Reshape"

  bottom: "inception_3c_double_3x3_1_bn"

  top: "res2b_bn_pre"

  reshape_param{

    shape { dim: -1 dim: 16 dim: 96 dim: 28 dim: 28 }

  }

}

layer {

  name: "Transpose1"

  type: "Permute"

  bottom: "res2b_bn_pre"

  top: "res2b_bn"

 permute_param {

  order: [0,2,1,3,4]

 }

}
```

以前将N帧图片放在batch通道，这里N做为时间通道,方便进行3D池化操作

### **3D时序池化**

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

HTP_1 size=2\*3\*3 stride=2\*2\*2

HTP_x size=2\*1\*1 stride=2\*1\*1

模块个数确定：若采样帧数为N，X=log2N

本文采用inception结构，N=16，一共有4个HTP模块

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

作为参考：

TSN的output size:(batch,channel,Height,Weight)

conv1:16\*64\*112\*112

conv5b:16\*1024\*7\*7

## **实验分析：**

### 1.精度比较：双流结构的比对

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

### 2.速率比较（只使用RGB）

![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)



![img](https://note.youdao.com/ynoteshare1/images/replace-img.png)

**圆圈的大小代表模型的大小**（由于是在TSN基础上加入无参数的3D池化，因此两者模型大小相等）可以看出，HTP-Net在速度、模型大小、精度多个维度上达到了很好的平衡。（右上角的小圆是理想结构）

## **总结思考：**

1.速率达到目前最高。但精度上相比于其它网络还有差距（单独RGB，UCF101上90.2%）最高是93.5%

2.双流法HTP（two-stream）能达到最高水平（96.2%）。但也表明还没有完全挖掘动态的信息。

**RGB+RGBdiff**可实现双流融合结合动态信息，同时也能实现端到端高效训练（模型扩大一倍，速度几乎不变）池化只采用了简单的平均池化，如果结合**注意力池化**等方式也许会更能挖掘动态信息



注：收录在MultiMedia Modeling: 25th International Conference, MMM 2019 版权未开放