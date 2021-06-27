---
title: AI书籍：神经网络与深度学习
date: 2021-06-27 15:02:02
tags: Book
img: /images/nndl.png
mathjax: true
---

### Bias-variance
Bias-variance 偏差和方差。

Bias：$\mathbb{E}_x[(\mathbb{E}_D[f_D(x)-f^*(x)])^2]$，即最优模型和所有数据集上学出来的平均性能的差。衡量模型的拟合能力。

Variance：$\mathbb{E}_x[(\mathbb{E}_D[f_D(x)-\mathbb{E}_D[f_D(x)]])^2]$，即模型在不同数据集上性能的方差。

减小bias：提高模型复杂度、减小正则化系数，增加特征数量。

减小variance：减小模型复杂度，提高正则化系数，引入先验，集成模型。个人认为增大数据集也可以减小方差。

Bias和variance之间的关系：

![图 1](https://i.loli.net/2021/06/27/4eD7Pq8LwMYSV2y.png)  


### PAC学习理论
泛化误差$G_D(f)=R(f)-R_D^{emp}(f)$，即在某个数据集上学到的$f$与最优$f$之间的差。根据大数定理，当数据集$D$趋于无穷大时，泛化误差为0.

PAC = probably approximately correct，可能近似正确。即研究以多大的可能性获得一个近似正确的模型。

设可能性为$\sigma$，近似正确界限$\epsilon$，则PAC可以表示为$P(R(f)-R_D^{emp}(f)\le \epsilon)\ge 1-\sigma$。书面表达为，模型泛化误差大于$\epsilon$的概率为$\sigma$。一般$\epsilon$和$\sigma$取0到0.5之间。

如果已经确定$\epsilon$和$\sigma$，那么对应需要的样本数量为$N(\epsilon,\sigma)=\frac{1}{2\epsilon^2}(\log |f| + \log\frac{2}{\sigma})$.

### Inductive Bias 归纳偏置
其实也就是机器学习中的各种假设，比如聚类问题中，我们假设特征空间上邻近的数据属于同一类。归纳偏置也称为先验。

