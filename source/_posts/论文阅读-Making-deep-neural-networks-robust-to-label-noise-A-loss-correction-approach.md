---
title: '论文阅读 Making deep neural networks robust to label noise: A loss correction approach'
date: 2021-05-25 21:34:36
tags: Paper, Label noise
mathjax: true
---
# 作者
Giorgio Patrini1,2, Alessandro Rozza3, Aditya Krishna Menon2,1, Richard Nock2,1,4, Lizhen Qu2,1

1Australian National University, 2Data61, 3Waynaut, 4University of Sydney

CVPR 2017

# 用人话概述本论文
1. 提出了两种用`transition matrix`来解决`class dependent label noise`的方法。分别叫`backword loss correction`和`forward loss correction`。

2. 用理论证明`backwrod`是risk-consistent + classifier-consistent。`forward`是classifier-consistent.

3. 明确给出两个假设，在假设成立时，可以估计出T，哪怕没有clean data。
   - 假设1：anchor point存在
   - 假设2：noisy data 足够多的情况下，base model可以学出noisy posterior
  
4. 这个论文的主要贡献我认为是给出了理论证明。

# 问题
1. 作者批评 Training convolutional networks with noisy labels
   - 最小化$tr(Q)$是一种启发式的方法
   - 有一个固定的预训练过程
   - 说实话有点勉强，forward也有一个学习T的过程啊，好不到哪去。

一个hypothesis的risk是其在某个数据集上loss的期望。

# 要点
1. `risk-consistent`就是having same risk，为什么`backward loss correction`是`risk-consistent`呢？因为$E_{x,y}\ell(y,f(x))=E_{x,\tilde{y}}\ell^{\leftarrow}(\tilde{y},f(x))$. 证明解析看下面


2. `risk-consistent`必然是`classifier-consistent`，后者的含义是学出来的classifier一致。由`backward loss correction`的`risk-consistent`可以推导出$\argmin_f E_{x,y}\ell(y,f(x))=\argmin_f E_{x,\tilde{y}}\ell^{\leftarrow}(\tilde{y},f(x))$。二者学出来的f一样，所以`backward loss correction`是`classifier-consistent`。

3. 如果T知道的时候，那么用`forward`或者`backward`都能够解决问题，但是如果不知道T，那么就需要先估计，论文提出，要估计T需要两个假设。
   - 数据集足够大，这能够保证anchor point的存在，anchor point指的是部分特殊数据，它们的clean posterior为1，即$p(Y=i|x_n)=1$。这种anchor point可以被用来很好的估计T，因为$p(\tilde{Y}=j|x_n)=\sum_{k=1}^K T_{kj}p(Y=k|x_n)$。由于anchor point只有一项为1，其余为0，所以anchor point的nosiy posterior $p(\tilde{Y}_j|x_n)=T_{ij}\cdot 1$。但实验中并不是直接使用$p(Y=i|x_n)=1$的点作为anchor point，因为你不知道clean posterior，而是取nosiy posterior最大的点。即$\argmax_x p(\tilde{Y}=i|x)$。 但实际上，也不是取最大的点，而是取97分位数。但是在CIFAR-100上又不知道为什么这样又不好，反而取最大值更好。
   - 除此以外，还需要一个假设，那么就是网络足够强大，能够学出合适正确noisy posterior $p(\tilde{Y}|X)$，否则估计的T还是不准的。

4. 论文那还讨论了，backward loss 的 Hessian 与 原始loss的一致，而且都是0，说明他俩的一阶收敛速度一样。当然这东西我就不做深入研究了。


# 证明
我曾经被backward的证明卡了非常之久，我曾经以为自己看懂了，但是直到今天我才真正看懂。

下面详细解释

![图 1](https://i.loli.net/2021/06/04/G4xT6rKPIwFEAjD.png)  

首先，定义backward $\ell^{\leftarrow}(\tilde{y},f(x))=T^{-1}\ell(y,f(x))$. 没问题，人家想证明定义就证明定义，但是要注意，$\ell(y,f(x))$在本文中并不是一个值，而是一个向量。其含义为$\ell(y,f(x))=[\ell(1, f(x)),\ell(2, f(x)),...,\ell(c, f(x))]^\top$.

接着，直接看proof。核心难点就是理解：$\mathbb{E}_{\tilde{y}|x}\ell^{\leftarrow}(f(x))$。$\mathbb{E}$的下标永远代表取遍所有值的平均值，$x$取遍所有值就是取遍所有样本$x$。$\tilde{y}$是一个one-hot向量，只有label为1，其余为0，取遍所有值则为取遍这个one-hot向量中的所有值。

所以可以改写为$\mathbb{E}_{\tilde{y}|x}\ell^{\leftarrow}(f(x))=\frac{1}{n}\sum_{i=1}^{n}\sum_{j=1}^{c}\tilde{\mathbf{y}}_{ij}\cdot\ell^{\leftarrow}(f(x))_j=\frac{1}{n}\sum_{i=1}^{n}\tilde{y}_{i}\cdot\ell^{\leftarrow}(f(x))_{y_i}$. 

把这里的$\mathbf{y}_{ij}$理解成一个矩阵，每行只有一个1。

把这里的$y_i$理解成一个数字，代表样本$x_i$对应的标签。

这样就不难理解，$\frac{1}{n}\sum_{i=1}^{n}\sum_{j=1}^{c}\tilde{\mathbf{y}}_{ij}\cdot\ell^{\leftarrow}(f(x))_j=\frac{1}{n}\sum_{i=1}^{n}\mathbf{y}_{i\cdot}\cdot T\cdot\ell^{\leftarrow}(f(x))$，也就对应了上了proof的第一个等号，后面的都easy。

注意transition matrix $T$的结构比较特殊，$T_{ij}$代表的是样本$i$翻转到$j$的概率，而$\mathbf{y}_{i\cdot}$是一个行向量，所以相乘正好等于$\frac{1}{n}\sum_{i=1}^n\tilde{\mathbf{y}}_{i\cdot}$。注意，并不是直接等于$\tilde{\mathbf{y}}_{i\cdot}$而是约等于所有样本的noisy label的平均值，等于noisy label的期望。各种原因真得自己想了。看懂这里，本论文没什么难点了。