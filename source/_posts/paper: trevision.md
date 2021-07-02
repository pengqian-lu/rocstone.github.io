---
title: 论文阅读-Are Anchor Points Really Indispensable in Label-Noise Learning
date: 2021-06-05 20:33:08
tags: Paper, Label noise
img: /images/blackboard.jpg
mathjax: true

---
本文写作非常清楚
1. transition matrix很重要，可以用来设计risk-consistent和classifier-consistent的分类器

2. transition matrix的估计需要anchor point，但是假设anchor point已知不太现实

3. 本文解决anchor point未知的情况下如何学习transition matrix的问题

4. 解决方法是用一个slack variable去修正学到的transition matrix

5. 具体方法分三个步骤

   1. 直接用CNN学习noisy posterior，用large noisy posterior的样本的posterior作为transition matrix，即$T_{i\cdot}=p(\tilde{y}|x_J), J=\argmax_j p(\tilde{y}_i|x_j)$.

   2. 这个T被用于学习一个reweight loss function. 在noisy data上用reweight loss学习等同于在clean data上用ce loss去学习，所以这个reweight loss 是risk-consistent的。证明见下面。在reweight loss上学习一个新的CNN2。

   3. 用CNN2为initial network，再次去学习reweight loss，只是这个时候reweight loss计算时用到的T不再是第一步计算出来的T，而是$T+\Delta T$，其中$\Delta T$是可以被神经网络学习的小变量。


# 证明
![图 1](https://i.loli.net/2021/06/05/fgUmLO1CVvXyqap.png)  

这个而证明最让人费解的就是第二个等号，很明显这个等式是通过除以和乘以同一个项$P_{\bar{D}}(X=x,\bar{Y}=i)$来保持等号成立的。

那么问题是为什么分子上明明应该是$P_D(X=x,Y=i)$却被作者写成了$P_D(X=x,\bar{Y}=i)$呢？这里应该就是作者写错了。因为你在看原论文公式(3)上面一行会看到原文写着
> By employing $\hat{P}(Y=y|X=x)/\hat{P}(\bar{Y}=y|X=x)$ as weigh, we ...

可以发现，确实权重的分子应该是clean posterior，在official code中，这个clean posterior就是network softmax layer的输出，下面的noisy posterior就是是T的转置乘以clean posterior了。