---
title: >-
  论文阅读：Training deep neural networks on noisy labels with bootstrapping
date: 2021-06-28 16:26:28
tags: Paper, Label Noise
img: /images/blackboard.jpg
mathjax: true
---

## 思想
bootstrapping：good model makes good prediction & using good prediciton as target trains a good model.

## 方法：
Soft bootstrapping: $L_{\mathrm{soft}}(q,t)=\sum_k[\beta t_K + (1-\beta)q_k]\log (q_k)$。其实就是加权prediciton和given label作为label来训练。$q$代表某个样本的prediction，$t$代表该样本的label。

Hard bootstrapping: $L_{\mathrm{soft}}(q,t)=\sum_k[\beta t_K + (1-\beta)z_k]\log (q_k)$。其实就是加权prediciton的最大值和given label作为label来训练。

其中$z_k=\bold{1}[k=\argmax_i q_i]$.

最后用EM算法分步迭代求解prediction和model。

