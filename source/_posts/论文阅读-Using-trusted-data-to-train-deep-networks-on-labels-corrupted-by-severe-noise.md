---
title: >-
  论文阅读 Using trusted data to train deep networks on labels corrupted by severe noise
date: 2021-05-26 15:17:53
tags: Paper, Label noise
mathjax: true
---
# 作者
Dan Hendrycks∗
University of California, Berkeley

Mantas Mazeika∗
University of Chicago

Duncan Wilson
Foundational Research Institute

NeurIPS 2018

# 用人话概括论文
1. 从前的论文一般假设对clean data完全不可知，本论文提出拥有少部分clean data是合理的，所以本论文的核心就在于如何利用者少部分clean data to mitigate the effects of label noise.

2. 通过一个证明，可以知道clean data的noisy posterior的均值可以作为T的估计

3. 有了这个T的估计，就可以用forward来处理label noise。同时clean data也能直接用于训练。

# 要点

假设有 a subset of clean data。所以本文的主题就是如何用小部分clean data来mitigate the effects of noisy labels.
  - 用 clean subset 来估计T
  - 学习f的时候不仅仅利用noisy dataset，还利用 clean subset


![图 1](https://i.loli.net/2021/05/27/faXGKQ6MqLxsl1z.png)  


通过这个结构图就可以看出来，这论文算法真太简单了

1. 用noisy dataset学一个net1

2. 用net1预测clean data的class，相当于获得clean data的noisy posterior

3. 对它们进行求平均，作为T

4. 在clean subset上学一个net2

5. 用forward loss correction 在noisy data上继续学net2

6. 输出net2

**重点1：**
Dataset seperable means: $P(Y=i|x)=0, i\neq y_n$

**重点2：**
Separability implies $Y\bot \tilde{Y}|X$。 即$Y$与$\tilde{Y}$相互独立 

**重点3：**
为什么能够用clean data的noisy posterior来近似T?

$\hat{C}_{ij}=\frac{1}{|A_i|}\sum_{x\in A_i}\hat{p}(\tilde{y}=j|x)=\frac{1}{|A_i|}\sum_{x\in A_i}\hat{p}(\tilde{y}=j|y=i,x)\approx p(\tilde{y}=j|y=i)$

这个推导成立需要几个条件
1. seperable 条件推导出第二步： $\frac{1}{|A_i|}\sum_{x\in A_i}\hat{p}(\tilde{y}=j|x)=\frac{1}{|A_i|}\sum_{x\in A_i}\hat{p}(\tilde{y}=j|y=i,x)$
2. $\hat{p}$是对$p$良好的估计+clean dataset够大，推导出第三步。

# 问题
1. Sukhbaatar et al. [24] introduce a stochastic matrix measuring label corruption, note its inability to be calculated without access to the true labels
   - 然而这个论文并不需要知道real label，所以这是在乱喷。

2. 没有说清楚correction strength到底是不是flip rate，如果是,100% flip rate下GLC还能有90+%的acc，这怎么可能？？？

3. 没有说清楚forward gold是不是用gold的方式做T估计，如果是，那forward gold和GLC很接近啊！仅仅是用不用clean做训练的区别，怎么可能有这么多的acc 差距？

