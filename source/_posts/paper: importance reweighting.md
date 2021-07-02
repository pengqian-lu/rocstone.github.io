---
title: '论文阅读：Classification with noisy labels by importance reweighting'
date: 2021-06-29 16:40:58
tags: Paper, Label noise
img: /images/blackboard.jpg
---

## main idea
这篇论文的主要思路其实是借鉴transfer learning，把noisy data当做是source domain，把clean data当做是target domain。

这篇论文解决的问题是：backward 算法虽然可以提供risk-consistant classifier，但是需要求逆。

解决的途径是：通过一系列推导证明得出，可以对noisy posterior reweighting来获得risk consistant classifier.

推导：懒得写了，看paper就行