---
title: '论文阅读：Combating label noise in deep learning using abstention'
date: 2021-06-29 19:49:16
tags: Paper, Label noise
img: /images/blackboard.jpg
mathjax: true

---

## main idea
思路非常简单，在prediciton的output中，增加一个量，叫$p_{k+1}$，这个量代表抛弃对应样本的概率。换句话说，他这是把reweighting的weight交给神经网络来学了。设$\tilde{p}_{k+1}=1-p_{k+1}$。

loss为$\ell\left(x_{j}\right)=-\tilde{p}_{k+1} \sum_{i=1}^{k} t_{i} \log \left(p_{i} / \tilde{p}_{k+1}\right)-\alpha \log \tilde{p}_{k+1}$