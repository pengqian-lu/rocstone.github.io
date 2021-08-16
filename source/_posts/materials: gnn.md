---
title: '材料：GNN'
date: 2021-07-24 15:59:17
tags: Paper, GNN
img: /images/blackboard.jpg
mathjax: true
---

1. Benchmarking Graph Neural Networks. 这篇论文提出了为什么cora之类的小数据集有缺陷。Issues with Prevalent Datasets.

2. Open graph benchmark: Datasets for machine learning on graphs. 这个似乎提出了real-world dataset. 

3. **NRGNN**: Learning a Label Noise-Resistant Graph NeuralNetwork on Sparsely and Noisily Labeled Graphs. 说CV方法不能用GNN上。假设数据独立同分布、假设有很多noisy label可以拿来用。

4. NRGNN这篇论文非常重要，我下面直接展开对他的讨论

    - 这篇论文是 link unlabel nodes with labeled nodes who have similar features. 并且labeled node应该要有更高的概率是clean的



