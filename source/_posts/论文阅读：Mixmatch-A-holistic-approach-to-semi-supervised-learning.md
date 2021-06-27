---
title: '论文阅读：Mixmatch: A holistic approach to semi-supervised learning'
date: 2021-06-19 16:29:57
tags: Paper, Label Noise
img: /images/blackboard.jpg
mathjax: true

---

要点：
1. Consistency Regularization，一个样本做了aug之后，只要语义没有改变，那么它的prediction应该与aug前的样本一样。这个约束可以作为正则项。
2. Entropy Minimization，让prediction的entropy尽量的小，就可以让分类器做出比较confidently的分类。

流程：
1. 对unlabeled data做数据增强，每个生成k张，把他们的prediction mean 作为pseduo labe。
2. 做mixup生成数据集
3. loss=ce loss for labeled data + MAE loss for unlabeled data.


$C_a^1=a$

$C_a^2=\frac{a*(a-1)}{1*2}$

$\lim_{n\rightarrow\infty} a_n=A$

$\lim_{n\rightarrow\infty} a_{n+1}=A$