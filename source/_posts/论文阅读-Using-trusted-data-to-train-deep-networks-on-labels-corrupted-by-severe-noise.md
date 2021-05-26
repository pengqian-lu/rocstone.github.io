---
title: >-
  论文阅读 Using trusted data to train deep networks on labels corrupted by severe noise
date: 2021-05-26 15:17:53
tags: Paper
mathjax: true
---
# 作者
Dan Hendrycks∗
University of California, Berkeley

Mantas Mazeika∗
University of Chicago

Duncan Wilson
Foundational Research Institute

# 要点

假设有 a subset of clean data。所以本文的主题就是如何用小部分clean data来mitigate the effects of noisy labels.
  - 用 clean subset 来估计T
  - 学习f的时候不仅仅利用noisy dataset，还利用 clean subset


通过这个结构图就可以看出来，这论文算法真太简单了

1. 用noisy dataset学一个net1

2. 用net1预测clean data的class，相当于获得clean data的noisy posterior

3. 对它们进行求平均，作为T

4. 在clean subset上学一个net2

5. 用forward loss correction 在noisy data上继续学net2

6. 输出net2

# 问题
1. Sukhbaatar et al. [24] introduce a stochastic matrix measuring label corruption, note its inability to be calculated without access to the true labels
   - 然而这个论文并不需要知道real label，所以这是在乱喷。

2. 没有说清楚correction strength到底是不是flip rate，如果是,100% flip rate下GLC还能有90+%的acc，这怎么可能？？？

3. 没有说清楚forward gold是不是用gold的方式做T估计，如果是，那forward gold和GLC很接近啊！仅仅是用不用clean做训练的区别，怎么可能有这么多的acc 差距？