---
title: '论文阅读：Label ensemble'
date: 2021-06-29 19:49:16
tags: Paper, Label noise
mathjax: true
img: /images/blackboard.jpg
---

## Temporal ensembling for semi-supervised learning
取一个网络，用aug1做训练，产生prediction，用aug2做训练，产生prediciton2，这两种方式产生的prediction应该接近，所以可以作为loss约束。

![图 1](https://i.loli.net/2021/06/30/fkZUcKsDQ4rveIV.png)  


## Dimensionality-driven learning with noisy labels

local intrinsic dimensionality (LID) 是用来反映data的离散情况，即clean data应该和K-nearest-neighbors距离较近，noisy data较远。

$\hat{\mathrm{LID}}=-(\frac{1}{k}\sum_{i=1}^k\log\frac{r_i(x)}{r_{max}(x)})^{-1}$

$r_i(x)$代表x与第i个邻居距离。


