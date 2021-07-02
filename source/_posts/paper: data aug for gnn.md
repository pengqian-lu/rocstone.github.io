---
title: 论文阅读：Data Augmentation for Graph Neural Networks
date: 2021-06-25 14:59:32
tags: Paper, GNN
img: /images/blackboard.jpg
mathjax: true

---

### Motivation：
有些边应该有的却没有，比如该cite的paper没cite。有些不该有的边却有，比如发垃圾邮件的人被当做是朋友。

Idea：remve noisy edges and add missing edges

### 方法：

1. 对于Modified-graph setting。用GAE来估计每条边的存在的概率，高的留下低的去掉。

2. 对于Original-graph setting。用GAE来生成每条边存在概率的估计，其实也就是一个M矩阵，大小和邻接矩阵A一样，然后用将两个矩阵合并，$P=\alpha M+(1-\alpha) A$，最后对$P$做伯努利采样，获得最终的邻接矩阵$A$。伯努利采样其实是对$P$稀疏化，大致思想是如果$P_{ij}$太小，就令其为0。如此可以做到end-to-end训练。
当然，最后的loss也要修改成2部分，一部分是prediciton loss，一部分是估计出来的M和A之间的差。

### Details：
1. 由于存在计算M并且要反向传播，所以复杂度是$O(N^2)$。解决办法是参考论文`Inductive
representation learning on large graphs`做到mini-batch训练。


### 重点：
1. 通过某种方式生成augmented graph, $f:\mathcal{G}\rightarrow\mathcal{G}_m$，然后直接用$\mathcal{G}_m$来训练和推理。这里的推理应该指的是测试或者说做prediction。


2. 通过某种方式生成augmented graph, $f:\mathcal{G}\rightarrow\mathcal{G}_m$，然后用$\mathcal{G}_m$来训练，用$\mathcal{G}$来测试。

3. 前者叫modified-graph setting，适用于graph在推理的时候不会变的情况。后者叫original-graph适用于graph会动态变化的情况。

### 实验
![图 1](https://i.loli.net/2021/06/25/jfhIDxW74ZAkngM.png)  

首先这个图很有学习价值，即查看两个参数会如何影响performance。

其次可以看出paper connection类的dataset cora和citeseer不适合remove edge，但是很适合add edges。

而social dataset FLICKR很适合remove edge。

这意味着我们其实需要看数据集做操作，不能简单的添加edge或者减少edge。

![图 2](https://i.loli.net/2021/06/25/tnaXC46JcOsDhRV.png)  

从这个图其实也看出，相同的问题。即GAE的prediction其实并不能非常好的决定什么时候该remove 什么时候该add edge。

### 读后感
1. 我们是不是也可以把noise data旁边的edge给他去掉？但是要用什么方式去感知他们是noisy data呢？
    - label 与旁边的不同，肯定是一点，但其实semi的时候，很可能旁边都没有其他节点。但是如果做label propagation，一会也肯定有了


