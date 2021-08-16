---
title: '论文阅读：gnn mutual information'
date: 2021-06-30 19:56:03
tags: Paper, Label noise
img: /images/blackboard.jpg
mathjax: true
---

- [Mutual Information Maximization in Graph Neural Networks](#mutual-information-maximization-in-graph-neural-networks)
  - [提出问题](#提出问题)
  - [提出方法](#提出方法)
  - [总结](#总结)
- [Graph Representation Learning via Graphical Mutual Information Maximization](#graph-representation-learning-via-graphical-mutual-information-maximization)
  - [提出目的](#提出目的)
  - [提出方法](#提出方法-1)
  - [互信息](#互信息)
  - [其他](#其他)
- [Learning Graph Representation by Aggregating Subgraphs via Mutual Information Maximization](#learning-graph-representation-by-aggregating-subgraphs-via-mutual-information-maximization)
  - [方法](#方法)
  - [我的想法](#我的想法)
- [LDMI: A Novel Information-theoretic Loss Function for Training Deep Nets Robust to Label Noise](#ldmi-a-novel-information-theoretic-loss-function-for-training-deep-nets-robust-to-label-noise)
- [Hierarchical Clustering Based on Mutual Information](#hierarchical-clustering-based-on-mutual-information)


## Mutual Information Maximization in Graph Neural Networks

### 提出问题
 However, the design of these new models has little analysis of the transformation of the adjacency matrix of the graph data. Besides, to the best of our knowledge, while complex approaches such as mutual information theory and expectation-maximization estimation yield good results, the current transformation of the adjacency matrix achieves little improvement on graph tasks.

没看懂，说什么邻接矩阵的变化没有被分析

### 提出方法
In particular, we first introduce an equation of mutual information for the aggregation and iteration. We then update the equation of aggregation and iteration for the growth of mutual information. We further explore one of simple approaches aiming at the growth of mutual information.

提出用于aggregation的互信息公式，然后更新公式来增加互信息，最后提出一种提高互信息的简便方法。


### 总结
这个论文写的是真的不清不楚，不愧是CCF C的，大概意思是要增加互信息，就要增加aggregation时用到的邻居数量。然后代码也不清不楚。


## Graph Representation Learning via Graphical Mutual Information Maximization
### 提出目的
This paper investigates how to preserve and extract the abundant information from graph structured data into embedding space in an unsupervised manner

无监督学习presentation


### 提出方法
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


### 其他
1. 推导基于假设：$p(h_i|x_i)$ is multiplicative. 因此$I(h_i, x_i)=\sum_j^{|n(i)|}w_{ij} I(h_i,x_j)$

2. 


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


## LDMI: A Novel Information-theoretic Loss Function for Training Deep Nets Robust to Label Noise

理论保证对CCN robust。效果是$L(x,\tilde{y})=L(x,y)+noise\ amount$，这就意味着在这个loss上训练和clean的数据上训练毫无区别。但是要注意，在clean的数据上用$L$来训练和cross entropy loss来训练，就不一定谁更好了，也许是CE loss更好呢？

$LDMI(w_1,w_2)=|det(Q_{w_1,w_2})|$, $Q_{w_1,w_2}$是矩阵形式的两个变量的联合分布，计算方式是$\mathrm{Mean}(W_1 W_2^{\top})$, 其中$W_1$由n个$w_1$的列向量组成。

证明看完了，非常容易，首先一定要假设这是CCN，这有这样才能做对应的化简。其次利用了行列式的特殊形式，即$det(AB)=det(A)det(B)$，这样才能把转移矩阵弄出来。


## Hierarchical Clustering Based on Mutual Information
$p_i(X)=prob(X=x_i)$，代表随机变量取$x_i$的概率。

$p_i(Y)=prob(Y=y_i)$，$$p_{ij}(XY)=prob(X=x_i,Y=y_j)$

$H(X)=-\sum_i p_i(X)\log p_i(X), H(Y)=-\sum_i p_i(Y) \log \sum_i p_i(Y)$

$H(XY)=-\sum_{ij} p_{ij}(XY) \log p_{ij}(XY)$.

$H(X|Y) = H(XY) - H(Y) = -\sum_{ij} p_{ij}(XY) \log p_{i|j}(X|Y)$

$I(X,Y) = H(X) + H(Y) - H(X,Y) = \sum_{ij} p_{ij} \log \frac{p_{ij}}{p_i(X)p_j(Y)}$

$I(X_1,X_2,...,X_n) = \sum_{i=1}^{n} H(X_i) - H(X_1,X_2,...,X_n)$

$I(X,Y,Z) = I(X,Y) + I((X,Y), Z)$

Sec 2.2 还介绍了算法信息论，基本上是关于要输出字符串$X$需要的最小输入长度是多少，即$K(X)$，它有一些很接近信息论的关系，比如$K(XY)\le K(X)+K(Y)$。

这paper感觉写的不行。。换句话说，算法没有清晰的介绍，到了Sec 3太多文字了，也没有算法图。懒得看了。

反思一下我需要什么，我需要的是，有没有理论能证明用MI来计算node feature之间的相似度很靠谱，有很棒的理论。另外，MI真都能用来算相似度吗？因为node feature应该是作为一个随机变量X的，那么第二个随机变量该是谁呢？还是X？I(X,X)？这不合理。

node feature similarity不靠谱的原因是：小数据集的feature太sparse了，能不想个好办法，解决下。因为GCN的做法其实也解决的sparse的问题，那就是把邻居的feature aggregate到自己身上。但是这是基于一个smoothing假设的。但有时候邻居跟自己也不是同类，这么做的话就会有问题。

