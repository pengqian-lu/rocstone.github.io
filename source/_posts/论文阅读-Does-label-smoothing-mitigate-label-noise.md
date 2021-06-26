---
title: 论文阅读 Does label smoothing mitigate label noise
date: 2021-05-27 09:19:04
tags: Paper, Label noise
img: /images/blackboard.jpg
---
# 作者
Michal Lukasik, Srinadh Bhojanapalli, Aditya Krishna Menon and Sanjiv Kumar

Google Research, New York

PMLR 2020

# 要点
1. `label smoothing` 就是修改label，往label 里面添加对称噪声，比如一个data的one-hot label是$[0,0,1,0]$，假设 smoothing factor $\alpha=0.1$, 则修改后的标签为$[0.025, 0.025, 0.925, 0.025]$。本论文发现，虽然看起来加强了 effects of label noise，但事实上这么做却反而是一种对抗label noise的方法。

2. `label smoothing`可以看做一种`loss correction`，因为$\bar{R}(\mathbf{f} ; S)=\frac{1}{N} \sum_{n=1}^{N} \overline{\mathbf{y}}_{n}^{\mathrm{T}} \ell\left(\mathbf{f}\left(x_{n}\right)\right)$
   - 其中$\left(\overline{\mathbf{y}}_{n}\right)_{i} \doteq(1-\alpha) \cdot \llbracket i=y \rrbracket+\frac{\alpha}{L}$


`label smoothing` 的这个形式其实和 `backward loss correction`很像

如果你已经看过我`Making deep neural networks robust to label noise: A loss correction approach`的文章，那就知道`backward`的形式是$\frac{1}{n}\sum_{i=1}^{n}\sum_{j=1}^{c}\tilde{\mathbf{y}}_{ij}\cdot\ell^{\leftarrow}(f(x))_j=\frac{1}{n}\sum_{i=1}^{n}\mathbf{y}_{i\cdot}\cdot T^{-1}\cdot\ell(f(x))$$.

所以为什么label smoothing能够起到正面作用？直接让$\mathbf{\bar{y}}_n=\mathbf{y}_{i\cdot}\cdot T^{-1}$，就起到最大作用了，直接等同于backward。
