---
title: >-
  论文阅读：Pseudo-Label : The Simple and Efficient Semi-Supervised Learning Method
  for Deep Neural Networks
date: 2021-06-26 20:20:54
tags: Paper, Semi-Supervised
img: /images/blackboard.jpg
mathjax: true
---

## 流程：
1. 用labeled data预训网络
2. 对unlabeled data做prediction，将posterior最大的类作为其soft label
3. 用soft label对unlabeled data计算logistic loss
4. logistic loss添加权重，epoch大，权重大
5. 用given label对labeled data计算logistic loss
6. 合并两个loss，用于训练

## 问题：
1. 看起来确实有效，但是论文是如何说明其有效性的，把posterior最大的类作为soft label这个操作等于`entropy regularization`. $H\left(y \mid x^{\prime}\right)=-\frac{1}{n^{\prime}} \sum_{m=1}^{n^{\prime}} \sum_{i=1}^{C} P\left(y_{i}^{m}=1 \mid x^{\prime m}\right) \log P\left(y_{i}^{m}=1 \mid x^{\prime m}\right)$

#tag