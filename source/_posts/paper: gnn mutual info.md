---
title: '论文阅读：gnn mutual information'
date: 2021-06-30 19:56:03
tags: Paper, Label noise
img: /images/blackboard.jpg
mathjax: true
---

## Mutual Information Maximization in Graph Neural Networks

### 提出问题
 However, the design of these new models has little analysis of the transformation of the adjacency matrix of the graph data. Besides, to the best of our knowledge, while complex approaches such as mutual information theory and expectation-maximization estimation yield good results, the current transformation of the adjacency matrix achieves little improvement on graph tasks.

没看懂，说什么邻接矩阵的变化没有被分析

## 提出方法
In particular, we first introduce an equation of mutual information for the aggregation and iteration. We then update the equation of aggregation and iteration for the growth of mutual information. We further explore one of simple approaches aiming at the growth of mutual information.

提出用于aggregation的互信息公式，然后更新公式来增加互信息，最后提出一种提高互信息的简便方法。


### 总结
这个论文写的是真的不清不楚，不愧是CCF C的，大概意思是要增加互信息，就要增加aggregation时用到的邻居数量。然后代码也不清不楚。


### Graph Representation Learning via Graphical Mutual Information Maximization
## 提出目的
This paper investigates how to preserve and extract the abundant information from graphstructured data into embedding space in an unsupervised manner



## 提出方法
To this end, we propose a novel concept, Graphical Mutual Information (GMI), to measure the correlation between input graphs and high-level hidden representations.

衡量graph和学到的representation之间的关系。

GMI generalizes the idea of conventional mutual information computations from vector space to the graph domain where measuring mutual information from two aspects of node features and topological structure is indispensable

GMI exhibits several benefits: First, it is invariant to the isomorphic transformation of input graphs—an inevitable constraint in many existing graph representation learning algorithms

对输入graph的图结构变化是不受影响的？？刚才还说要衡量拓扑结构的互信息呢！

With the aid of GMI, we develop an unsupervised learning model trained by
maximizing GMI between the input and output of a graph neural encoder.

这句话说的很清楚了，最大化input feature和representation之间的互信息，来做无监督学习

![图 1](https://i.loli.net/2021/07/01/Sw56bO1iWTPCpmA.png)  


$I(h_i;X_i)=\sum\sum p(h_i,X_i)\log\frac{p(h_i,X_i)}{p(h_i)p(X_i)}=\sum_j^{i_n} w_{ij}I(h_i;x_j)=\sum_j^{i_n} w_{ij}I(h_i;x_j) + I(w_{ij};a_{ij})$

$w_{ij}=\sigma(h_i^\top h_j)$

$a_{ij}=A_{ij}$


### 互信息
$I(X;Y)=H(X)+H(Y)-H(X,Y)=-\sum p(x)\log p(x) -\sum p(y)\log p(y) + \sum\sum p(x,y)\log p(x,y) =\sum\sum p(x,y) \log\frac{p(x,y)}{p(x)p(y)}$



## Learning Graph Representation by Aggregating Subgraphs via Mutual Information Maximization


### 方法
To give a reasonable constraint on representation learning, we can guide our model by maximizing the mutual information between the original graph and the reconstructed graph representations rather than between graph and node representations as in previous works.

上篇论文是最大化feature和representation之间的互信息，这篇是最大化original graph和 reconstructed graph representation


### 我的想法
综上，目前mutual information用于GNN都是用在相同节点不同程度的representation上。

解决label noise in GNN，思路：remove noisy labels。

1. 如果所有data的label已知，那么我们可以根据mutual information来计算每个节点和相邻节点之间的互信息

2. 然后把MI小的data当做是noisy label来去除。但可惜的是我们无法计算$y_1$和$y_2$之间的MI.

换个思路。

1. 在labeled data上pretrain一个GCN。

2. 以maximize mutual information between label prediciton of neighbors 作为目标函数，来训练神经网络。以此为目标可以correct label。

3. 以prediciton作为pseudo label来训练GCN，参考`seudo-Label : The Simple and Efficient Semi-Supervised Learning Method for Deep Neural Networks`

