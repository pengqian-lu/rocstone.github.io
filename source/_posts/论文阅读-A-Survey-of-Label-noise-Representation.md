---
title: 论文阅读 A Survey of Label-noise Representation
Learning: Past, Present and Future
date: 2021-05-04 19:20:41
tags: Paper, Survey, Label noise
mathjax: true
---

## Introduction
1. Label noise 最早被讨论的论文
2. 一些例子论述它广泛存在

## Motivation

3. 讨论相关survey以及为什么它们不够全面
4. 分析为什么noisy label会影响dnn
5. 对其进行分类
6. LNSL (label noise statistic learning) 和INRL的不同：
   1. 假设空间不同，介绍与sec 3.5.2
   2. 解不同，介绍于sec 6
   3. INSL不适合对付大规模数据
7. 与现有的INRL survey不同
   1. 现有的只讲方法
   2. 这篇还会讲更多理论上的理解
8. 框架
   1. sec 2 related work
   2. sec 3 定义，核心问题，对数据、目标、优化的分类
   3. sec 4 transition matrix based methods
   4. sec 5 修改目标函数的方法
   5. sec 6 利用DNN网络特点来处理label noise
   6. sec 7 futural directions

## Related works
1. 1988年Angluin提出学习算法可以处理noisy data，但是noise rate不能超过50%。
    - 这其实是有问题的，如果学习算法直接就能处理，那又何必提出新的方法呢？
2. Learning with noisy labels 提出了一个unbiased risk estimator，用于处理2分类情况西的label noise问题。对于随机标签噪声，它给出了risk minimization的gaurantees
    - [] 这个论文要看一下，看看这个所谓的理论保证是什么？
    - [x] unbiased的又是什么？
3. Classification with noisy labels by importance reweighting 提出用anchor points 来估计noise rate，并且用importance reweighting来设计loss function.
    - [] 看看如何以及为什么用ap就能估计noise rate 
    - [] 看看importance reweighting是什么？为什么能有效解决这个问题
4. Training convolutional networks with noisy labels 提出在softmax layer上面设计一个linear noise adaption layer
   - [] 看一下为什么这样做有效？
5. Auxiliary image regularization for deep cnns with noisy labels提出可以用辅助的正则化策略来选择可靠的label，这个正则化策略利用了mutual context information。
   - [] 为什么这样子可以选出可靠的label
   - [] mutual context information是什么？它是怎么做为正则化项的？
6. Training deep neural-networks using a noise adaptation layer 提出可以用一个 onliner noise adaption layer
   - [] 同样的问题，解决了什么问题，如何解决的
7. Making deep neural networks robust to label noise: A loss correction approach经典必看
   - [] 为什么有效？
8. Co-teaching: Robust training of deep neural networks with extremely noisy labels
   - [] accumulated error是什么？
   - [] 为何有效
9. mixup:Beyond empirical risk minimization
   - [] 为什么把label和data做混合能有效？
10. Generalized cross entropy loss for training deep neural networks with noisy labels
   - [] 了解里面的理论推导
11. Unsupervised label noise modeling and loss correction 这个paper要看一下，似乎很有趣
12. Using pre-training can improve model robustness and uncertainty
   - [] 为什么pretrain会有效？无论怎么样pretrain都有效吗？
13. Self: Learning to filter noisy labels with self-ensembling 逐渐找出noisy labels
   - [] 听起来很有趣
14. Dividemix: Learning with noisy labels as semi-supervised learnin 把数据集动态分成有标签和无标签集合
    - [] 听起来很有趣
15. Curriculum loss: Robust learning and generalization against label corruption
   - [] 我觉得这个方向很有前途
16. **Bayes optimal贝叶斯最优是什么？**
17. 相关领域
    - 无监督学习，unlabel data + labeled data which is fully clean
    - Positive-unlabeled Learning (PUL), positive labeled and unlabeled data
    - Unlabeled-unlabeled data. 两个数据集的数据混在一起做二分类。
18. Risk 定义
   - $R_{\ell,D}(f_\theta)=\mathbb{E}_{(x,y)\sim D}[\ell(f_\theta(x),y)]$
   - 一个hypothesis的risk，就相当于它在数据集上的loss的期望。
   - 有条件，(x,y)是independently drawn from clean distribution D.
19. Learning from binary labels with instance-dependent corruption 说明了instance dependent transition matrix是无法学出来的
20. anchor point的定义是：
   - $p(Y=y_i|x_i)=1$
   - ✔ 我现在很奇怪，因为我认为每个data都满足上述条件才对？？
     - 这里的其实是$\hat{p}$，也就是神经网络的输出，或者说我们估计的$p$，所以这算是符号不严谨。
   - 如果ap找不到，那么就用$x_i=\argmax_x p(\bar{Y}=i|x)$来作为ap.
   - 用ap可以学出T，用T+loss corection 可以构建risk-consistent estimator，用T+hypothesis correction可以构建classfier-consistent estimator
   - [] 这两个是啥？定义
21. 学习label noise的三个重要元素
    1.  从数据层面，研究transition matrix
          - class dependent  $T =T_{ij}=p(\bar{Y}=e_j|Y=e_i,x_i)$
          - 线性
              - Training convolutional networks with noisy labels，2015
              - 论文提出，其实label noise还有一种outliers，也就是不属于数据集中的任何一类，但是却被错误标记成了其中一类
              -  ![网络结构](/images/8ca5d670c81290d50f7aed5509b64a7a4483451c717ddd6b5570e8d1b433382d.png)  
              -  直接学出T、clean posterior、noisy posterior
              -  推导了一下为什么这样做可以让model学出clean posterior
              -  还解释了为什么直接这样学不行，理由是这样学会有无穷解，无法保证学出来的T可靠
              -  做强假设，T是主对角dominant，推导出$tr(T*)\le tr(T)$,所以可以以此约束要学习的T，即不断让T的trace越来越小，实际上论文直接在T上家weight decay，因为这样同样能起效。
          - 非线性
               - Training deep neural-networks using a noise adaptation layer
               - 一样要加adaption layer，但是不做强假设，用em算法来学。
               - 写的并不清楚，也没有用图很好的展示$w_{noise}$是什么，先不管了。
          - backward和forward
               -  Making deep neural networks robust to label noise: A loss correction approach
               -  backward: $\ell^{\leftarrow}(\hat{p}(\tilde{y}|x))=T^{-1}\ell(\hat{p}(\tilde{y}|x))$
               -  forward: $\ell^{\rightarrow}(\hat{p}(\tilde{y}|x))=\ell(T^{\top}\sigma(h(x)))$
               -  证明了backward是unbiased estimator，即$\mathbb{E}_{x,\tilde{y}}\ell^{\leftarrow}(y,\hat{p}(y|x))=\mathbb{E}_{x,y}\ell(y,\hat{p}(y|x))$
               -  **这种特性也被成为risk consistent**
               -  但是forward只能证明用它学出来的分类器和在clean data上学出来的最优分类器一样好,这种特性也被称为**classifier consistent**
          -  gold correction: Using trusted data to train deep networks on labels corrupted by severe noise
                -  除了noisy dataset，还有一个clean subdataset
                -  用noisy dataset训练出一个分类器先
                -  然后在clean subdataset上测试这个classifier，用来估计T
                -  $T_{ij}=1/|A_i|\sum_{x\in A_i}\hat{p}(\tilde{y}=j|x)$
                -  论文提到，如果数据集是separable（也就是y is deterministic given x），那么学出$p(\tilde{y}|x)$有可能的。
          -  label smooth: Does label smoothing mitigate label noise
                -  label smooth 可以看做是正则化
                -  我发现label smooth其实就是另一种correction，首先来看label smooth的定义$\bar{R}(f)=\frac{1}{N}\sum_{n=1}^{N}\bar{y}_n^T\ell(f(x_n))$，相比之下，原来的empirical risk是$R(f)=\frac{1}{N}\sum_{n=1}^{N}\ell(y_n,f(x_n))$。可以发现就是做了一个类似reweight的东西。而且$\bar{y}_n$是一个向量，长度为L也就是类别数量，$(\bar{y}_n)_i=(1-\alpha)[i=y]+\frac{\alpha}{L}$。
                -  实验值在symmetric上做，这不是搞笑吗，其实这个label smooth就可以看成是假设T是symmetric了。我只能说这实验很没有说服力，而且也没有比FC更好，这论文有什么意思吗？
          -  trevision
                -  论文用reweight的办法来代替求backward中的$T^{-1}$. 但是这个reweight的推导写的是错的，应该是$\sum_x\sum_i P_{\tilde{D}}(X=x,\tilde{Y}=i)\frac{P_{D}(Y=i|X=x)}{P_{\tilde{D}}(\tilde{Y}=i|X=x)}\ell(f(x),i)$。
                -  [] 下面看到泛化误差的证明了，generalization error. 额细节还是挺麻烦的，先不看了。
    2.  从目标函数层面，设计一个noise-tolerant损失函数，以此训练出一个robust classifier，并且能够证明这个classifier会收敛到能在clean data上学出来的classifier（不太可能把）
          - 根据理论证明，如果dataset足够大，hypothesis set足够大，那么学到的分类器可以接近clean data上学到贝叶斯最优分类器
          - $\ell + r$ 正则化
          - $\sum_i w_i\ell_i$reweight
          - 全新的loss
    3.  从优化角度，利用dnn的记忆效果
22. 
