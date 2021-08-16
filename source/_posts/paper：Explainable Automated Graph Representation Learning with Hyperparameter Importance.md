---
title: '论文阅读：Explainable Automated Graph Representation Learning with Hyperparameter Importance'
date: 2021-07-22 14:21:14
tags: Paper, GNN
img: /images/blackboard.jpg
mathjax: true
---


## 问题
AutoML可以用来学超can，但是没有工作去研究超参之间的重要性。

Subgraph：在graph上选一个起点，然后random walk几次，就可以获得一个sub graph，一共$s$个。论文认为， 每次选择的起点应该尽可能在不同的区域，也就是拥有不同的label，这样方便用各个subgraph感知graph的各种特征。

每个subgraph上都要执行算法$R$，但是次数不一样$t_i$次，如果subgraph和原始graph越相似，$t_i$越小。

如何衡量subgraph与grpah的相似度？论文取了6个值（根据`Efficient comparison of massive graphs
through the use of’graph fingerprints`），据说这6个值不仅能代表graph feature，而且还可以解释，分别是节点数、边数、三角节点数、全局聚类系数$\frac{3*(# of trangles)}{# of triplets}$，最大度数、最大连通子图个数。其中triplets的定义是：三个节点之间有2个以上条边，则该结构被称为triplets。

feature有了，如何衡量graph和subgraph的相似性呢？论文使用的是`Canberra Distance`，定义非常简单，设$f\in\mathbb{R}^6$为graph的feature，$f^i$为subgraph的feature。二者之间的堪培拉距离为$g^i=d(f^i,f)=\sum_{k=1}^{6}\frac{|f_k^i-f_k|}{|f_k^i|}+|f_k|$.

不明白为什么要decorrelation between Hyperparameter and features with sample reweighting. 