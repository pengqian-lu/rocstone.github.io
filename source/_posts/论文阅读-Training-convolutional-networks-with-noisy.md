---
title: '论文阅读: Training convolutional networks with noisy labels'
date: 2021-05-24 14:14:30
tags: Paper, Label noise
mathjax: true

---

# 作者、级别
Sainbayar Sukhbaatar

Department of Computer Science, New York University

ICLR 2015

# 用人话概括本论文
1. 论文提出，既然我们的数据集只有noisy label，那么就直接去学noisy label的后验概率好了。因为我们知道noisy label和clean label的关系是由一个转移矩阵Q确定的，所以只要学好了noisy label，就能把Q学出来，知道了Q和noisy label，也就能学出来我们想要的clean posterior。

2. 用一个可学习的linear layer来代表这个Q，把Q放在base model的上面，用来模拟Q进行污染的标签的过程。

3. 虽然这个网络确实能够表达clean label noisy label之间的关系， 但是如何保证我们学出来的Q就是真实的Q呢？论文提出，直接这样学是学不出来的，因为同时学Q和noisy posterior，并不能保证Q是对的，换句话说，哪怕Q学的不对，noisy posterior也可以学对，因为noisy label并不能直接约束Q收敛到Q*。

4. 论文首先证明，如果学到的Q是真实的Q，那么必定能学到clean posterior。

5. 论文然后证明，$tr(Q)\ge tr(Q^*)$，所以可以以此为约束，对Q进行最小化，从而让$Q$收敛到$Q^*$.

6. 算法执行分两步，先把linear layer设置成单位矩阵，让base model学习到noisy posterior，作为一个好的起点，然后再同时学习base model和Q，方便Q收敛到$Q^*$.

7. 在实验中，论文提出了一种非常有意思的adversial label noise。label noise的基本假设就是样本容易翻转到其它相似的class上，如何定义相似呢？论文用一个model学习clean posterior，然后看样本在哪个class上的posterior最大（当然要除了最大的那个），那么就意味着样本有更高的概率翻转到那个类。这个clean posterior做平均值，就可以用来当做Q了。这一点可以利用到我们未来的噪声生成上。



# 符号
1.  $y ^*$代表true label 
2.  $q^*_{j,i}=p(\tilde{y}=j|y^*=i)$
    - 属于i类，被翻转到j类的概率
3. 注意，常用的transition matrix $T_{ij}=P(y=i|\tilde{y}=j)$. 但是使用时要转置，即$T^{\top}P(y|x)=P(\tilde{y}|x)$. 所以q相当于是转置后的T.


网络特点：base mode - linear layer Q - noisy posterior


![图 1](https://i.loli.net/2021/05/24/1oiSfbURyxptXrI.png)  


可以这个论文的思路是，既然我们只有noisy label，那么我们就去学noisy label的分布，并且已经知道noisy label和clean label的关系，所以学好了noisy label，就能学好clean label。

![图 2](https://i.loli.net/2021/05/24/FRHgvdsEMfuqelw.png)  

这个图展示了训练的过程。先把Q设置成单元矩阵，在训练一会之后，再允许Q进行学习。这样能让base moel先学到noisy posterior上，此时base model相当于找到了一个较好的起点，然后再去学Q，会靠谱很多。

但是我们想学的是clean posterior，如何保证我们的base model真的能学到它呢？

下面其实开始证明。先介绍混淆矩阵$C$.

$c_{i j}:=\frac{1}{\left|S_{j}\right|} \sum_{n \in S_{j}} \hat{p}\left(y^{*}=i \mid \mathbf{x}_{n}, \theta\right)$

首先，$S_j$代表一个子集，其中所有样本都真实属于第j个类。

$\hat{p}\left(y^{*}=i \mid \mathbf{x}_{n}, \theta\right)$代表真实属于第j类，预测它真实属于第i类的概率，也就是base model的输出。

所以$c_{ij}$就代表，所有属于第j类的样本，它们被base model预测为第i类的平均概率。

**所以我们希望C趋近于一个单位矩阵**

## 为什么这样的model可以让C趋近于一个单位矩阵？

同时我们还能定义
$\tilde{c}_{ij}=\frac{1}{|S_{j}|}\sum_{n\in S_{j}}\hat{p}(\tilde{y}=i | x_{n}, \theta)$

这就所有原本属于j类的data，它们被model预测为i类的概率。

所以有$\tilde{C}=QC$. 

通过推导证明，得出$\tilde{C}=QC\rightarrow Q^*$，所以只要让$Q=Q^*$，就能让$C=I$。

但是直接学习，并不能保证学出来的$Q$能够等于$Q^*$。比如可能学出来的$C=Q^*$，$Q=I$，这样学到的C就不对。实际上这里有无穷解，所以我们要对Q进行约束。

## 约束

通过证明可以知道，$tr(Q)\ge tr(Q^*)$，并且当$Q=Q^*, C=I$时，能够取等号。所以以在目标函数中添加一个最小化$tr(Q)$的项，实际上直接在$Q$上做weight decay，就能够最小化$Q$。Weight dacay的参数设置为0.1，但是也可能更小。

## 数据集处理
For all datasets, the only preprocessing step is mean **subtraction**, except for SVHN where
we also perform **contrast normalization**. 

For ImageNet, we use data augmentation by **taking random
crop of 227x227** at random locations, as well as **horizontal flips with probability 0.5**.

ImageNet上做了一个很厉害的adversial label noise.

First, we train a
normal convnet on clean data and measure its confusion matrix. This matrix gives us a good metric
of which classes more likely to be confused. Then this matrix is used for constructing Q∗ so that
40% of labels are randomly flipped to other similar labels.

## 实验图
![图 3](https://i.loli.net/2021/05/25/dmvB9AbeuxiRHcO.png)  

这个图c用图像的方式来展示学到的transition matrix，我觉得很高明，因为数字上的大小其实感觉不出来，但是用图就会让你觉得非常的接近。

