---
title: 论文阅读-Masking A New Perspective of Noisy Supervision
date: 2021-06-08 21:22:17
tags: Paper, Label noise
---

论文main idea很简单，transition matrix很多项是无用的，比如你知道马这个类基本上不可能被翻转成桌子。所以$C\times C$大小的transition matrix其实只有一部分有效，但是有效的部分到底是谁？这个得借用人类的知识当先验。

![图 3](https://i.loli.net/2021/06/08/3VGdofqmvLYPnZa.png)  

比如这个图就代表了三种可能的翻转情况

1. 某几个类很容易翻转到其他类
2. 每个类都容易翻转到下一个类
3. 每个类的动物都有概率翻转到同科其他动物上，但是不会翻转到其他科的。


但是具体如何利用等等，我没看懂