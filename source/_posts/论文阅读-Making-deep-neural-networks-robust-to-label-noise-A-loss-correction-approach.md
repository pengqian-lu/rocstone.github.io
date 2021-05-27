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
1. `risk-consistent`就是having same risk，为什么`backward loss correction`是`risk-consistent`呢？因为$E_{x,y}\ell(y,f(x))=E_{x,\tilde{y}}\ell^{\leftarrow}(\tilde{y},f(x))$.

2. `risk-consistent`必然是`classifier-consistent`，后者的含义是学出来的classifier一致。由`backward loss correction`的`risk-consistent`可以推导出$\argmin_f E_{x,y}\ell(y,f(x))=\argmin_f E_{x,\tilde{y}}\ell^{\leftarrow}(\tilde{y},f(x))$。二者学出来的f一样，所以`backward loss correction`是`classifier-consistent`。

3. 如果T知道的时候，那么用`forward`或者`backward`都能够解决问题，但是如果不知道T，那么就需要先估计，论文提出，要估计T需要两个假设。
   - 数据集足够大，这能够保证anchor point的存在，anchor point指的是部分特殊数据，它们的clean posterior为1，即$p(Y=i|x_n)=1$。这种anchor point可以被用来很好的估计T，因为$p(\tilde{Y}=j|x_n)=\sum_{k=1}^K T_{kj}p(Y=k|x_n)$。由于anchor point只有一项为1，其余为0，所以anchor point的nosiy posterior $p(\tilde{Y}_j|x_n)=T_{ij}\cdot 1$。但实验中并不是直接使用$p(Y=i|x_n)=1$的点作为anchor point，因为你不知道clean posterior，而是取nosiy posterior最大的点。即$\argmax_x p(\tilde{Y}=i|x)$。 但实际上，也不是取最大的点，而是取97分位数。但是在CIFAR-100上又不知道为什么这样又不好，反而取最大值更好。
   - 除此以外，还需要一个假设，那么就是网络足够强大，能够学出合适正确noisy posterior $p(\tilde{Y}|X)$，否则估计的T还是不准的。

4. 论文那还讨论了，backward loss 的 Hessian 与 原始loss的一致，而且都是0，说明他俩的一阶收敛速度一样。当然这东西我就不做深入研究了。

5. 