---
layout:     post
title:      Can Spatiotemporal 3D CNNs Retrace the History of 2D CNNs and ImageNet
subtitle:   人体动作识别论文阅读
date:       2018-01-05
author:     Mily
header-img: img/post-bg-cook.jpg
catalog: true
tags:

    - action recognition
---

## **摘要**

- ResNet-18在UCF-101, HMDB-51, and ActivityNet数据集上训练会出现过拟合，但是 **Kinetics数据集不会过拟合**

- Kinetics数据集有足够的数据可以使得ResNet达到**152层**，类似于2D ImageNet

- Kinetics数据集上预训练的**ResNeXt-101**模型在UCF-101数据集上准确率达到了**94.5%**

  ![clipboard(4)](/../img/2018-01-05-Can-Spatiotemporal-3D-CNNs-Retrace-the-History-of-2D-CNNs-and-ImageNet/clipboard(4).png)
  
  ![clipboard(3)](/../img/2018-01-05-Can-Spatiotemporal-3D-CNNs-Retrace-the-History-of-2D-CNNs-and-ImageNet/clipboard(3).png)

3D ResNets在Kinetics验证数据集上top-1至top-5平均准确率，随着网络层数增加而上升。当到达**152**层时，呈现饱和状态，这与2D ResNets 在ImageNet的表现类似

![clipboard(5)](/../img/2018-01-05-Can-Spatiotemporal-3D-CNNs-Retrace-the-History-of-2D-CNNs-and-ImageNet/clipboard(5).png)

![clipboard(2)](/../img/2018-01-05-Can-Spatiotemporal-3D-CNNs-Retrace-the-History-of-2D-CNNs-and-ImageNet/clipboard(2).png)

### **网络加深分析：**

![clipboard(1)](/../img/2018-01-05-Can-Spatiotemporal-3D-CNNs-Retrace-the-History-of-2D-CNNs-and-ImageNet/clipboard(1).png)

![clipboard(6)](/../img/2018-01-05-Can-Spatiotemporal-3D-CNNs-Retrace-the-History-of-2D-CNNs-and-ImageNet/clipboard(6).png)

![clipboard](/../img/2018-01-05-Can-Spatiotemporal-3D-CNNs-Retrace-the-History-of-2D-CNNs-and-ImageNet/clipboard.png)

### **总结**

- 以往的3D ConvNet模型不如two stream 的原因在于数据集太小，不能满足3D卷积较多参数的要求，容易出现过拟合情况。随着Kinetics大数据集的出现，使得3D ConvNet发挥出它的功效。
- I3D是个很好的证明（inception：22 layers）
- 在kinectics数据集下，现在3D ConvNet的层数极限为152层，与ImageNet相似，迁移学习可以很好的运用。
- 以ResNet为例，可以看出层数越深，效果越好