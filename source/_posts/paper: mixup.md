---
title: >-
  论文阅读：mixup: Beyond empirical risk minimization
date: 2021-06-28 16:59:23
tags: Paper, Label Noise
img: /images/blackboard.jpg
mathjax: true
---

## 方法 

1. sample two different data $x_i, x_j$ from $D$. Create a new data $tilde{x}=\gamma x_i + (1-\gamma) x_j$

2. train model with $\tilde{x}$ and $\tilde{y}=\gamma y_i + (1-\gamma) y_j$

3. 其中$\gamma\sim \mathrm{Beta}(\alpha,\alpha), \alpha\in(0,\infty)$


## 为何这么做可以？
来源于paper，Vicinal risk minimization。

大概的思路是，用一个近似的分布$P_{est}(x,y)来代替$empirical risk minimization中的$P_{emp}(x,y)$。

具体分析参考另一篇论文HOW DOES MIXUP HELP WITH ROBUSTNESS AND GENERALIZATION?

## HOW DOES MIXUP HELP WITH ROBUSTNESS AND GENERALIZATION?

根据一系列的推导证明，可以发现mixup等同于standard loss+正则化，特殊的是这个正则化是加在$\Delta f_\theta(x_i)$和$\Delta^2f_\theta(x_i)$上的.

