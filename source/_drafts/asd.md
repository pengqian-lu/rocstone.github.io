1. mutul information是用来鉴定两个分布的依赖关系的，假如两个分布相互依赖，那么它的互信息就会很大，反之就会很小。
   - 也许可以用到graph里面去，因为我们猜测，相邻节点的的label也是相互依赖的。
   - 但是这个方法能解决问题吗？label 相互依赖真的没被解决吗？我要考虑考虑。
   - 如何计算两个prediction的互信息也是个问题。
2. data augmentation是很好的办法，那么graph上能不能做呢？
   - 能不能transform feature？
       - feature如果是文本，那么没可能做aug
   - 能不能transform node connection？
       - 可以考虑做dense，即把二度邻居变成一度邻居
           - 理由：想不出来
3. GCN是$\sigma(\tilde{A}XW)$，把$\tilde{A}W$看成整体，忽略$\sigma$，那就变成了$QW$，其中$Q\in{\mathbb{R}^{nd}}$，$W\in\mathbb{R}^{dc}$。这时候就会发现，其实W是施加在每个节点上的，但同时每个节点通过aggregation，其实是本节点和相邻节点的加权和。所以一个label错了，就会影响到所有其他节点。

4. entropy regularization其实也可以直接用到label noise里面去，但是我怀疑和label correction可能一样，而且没有novelty。

5. mixup [[paper: mixup.md]]] 是把label相加，feature相加，和aggregation相当于feature相加，那么此时为何不做label相加呢？因为我们不知道label，有没有办法知道呢？有的，那就是把prediciton作为label。把prediction作为label的方法叫entropy regularization [[paper: pseudo label for semi.md]]. 具体需要深入研究。


### 想法1：不太可行

解决label noise in GNN，思路：remove noisy labels。

1. 如果所有data的label已知，那么我们可以根据mutual information来计算每个节点和相邻节点之间的互信息

2. 然后把MI小的data当做是noisy label来去除。但可惜的是我们无法计算$y_1$和$y_2$之间的MI.

### 想法2：感觉可以

1. 在labeled data上pretrain一个GCN。

2. 以maximize mutual information between label prediciton of neighbors 作为目标函数，来训练神经网络。以此为目标可以correct label。

3. 以prediciton作为pseudo label来训练GCN，参考`seudo-Label : The Simple and Efficient Semi-Supervised Learning Method for Deep Neural Networks`


### 想法3：mixup for GNN

mixup可以被挺好的用来处理label noise问题，
因为他可以被看作是一种正则项。

但是mixup不能直接用到graph semi-supervised learning上，因为大量的label都不知道。

所以先pretrain一个GNN，然后用prediction做pesudo label。

现有的GNN其实和mixup有相似之处，比如他们都会把部分X做混合。不同之处在于mixup会把label也做对应的混合，但是GNN不会。

因此我们可以用一个GNN来混合X，另一个GNN来混合Y并作为目标，这样就构造出了mixup for GNN。




但是放到graph上却不太行，因为现在graph的node classification 都是semi-semi-supervised。有个而semi-supervised learning有个常见的做法，就是把predicted label作为target label来训练，比如这篇文章《Pseudo-Label : The Simple and Efficient Semi-Supervised Learning Method for Deep Neural Networks》

所以我想，结合一下mixup + pseudo-label就可以解决 semi + label nosie了。mixup和gnn也可以结合，因为gnn的aggregation其实就是在mix feature了


### 想法4：feature similarity based label correction

上两周主要在看老师发的论文，研究mute information的使用，在纸上推推写写的时候，产生了一个比较简单的idea，并且做了一些实验验证了这个想法的有效性。不幸的是，昨天我在找baseline对比的时候，发现了这篇发表于今年KDD的文章${[1]}$。它的方法虽然不知为何，实验效果我验证出来很差，但是idea和我的很相似。我的想法是用feature相似的node来做soft voting，假设transition matrix是diagonally dominant，那么soft voting肯定有效，因为一般GNN都假设feature相似则大概率属于同类。

结果KDD的idea很类似，即如果一个labeled node和一个unlabeled node的相似度很高，那就在它们之间加边。然后理论推导证明，这么做就可以提高model prediction的正确性。

不过我还不死心，毕竟实验结果出来，kdd那个就是不行，而且它还有些奇怪的trick，我准备下面看看能不能再看看有没有改进的点，把我对方法做成是对kdd方法的改进，这样应该还是有意义的。

idea的书面介绍和实验结果在pdf内。


idea:

Assume transition matrix is diagonally dominant. For any noisy data $(x_i,\tilde{y_i}, y_i)$, 
if we can sample a set of noisy data $S=\{(x_j, \tilde{y_j}, y_j)| y_j = y_i \}$, which also belong with $y_i$, then we can correct the noisy label with hard voting. It is guaranteed that the $\tilde{y_i}$ will be corected as $y_i$ according to our assumption if the sample size of $S$ is large enough.

details:

But there is a problems: how to find such sample $S$ when we can not observe the real labels?

A widely used assumption in GNN is that if two nodes have high feature similarity, they are more likely to have the same label. Thus, we can find the sample according the feature similarity among nodes. I measure the similarity with GAE, a popular node representation learning method. 

Another problems is that we only have a few of labeled data in GNN dataset, which means the size of $S$ tends to be small. Thus, my label correction method includes two steps: 1) correct noisy labels by soft voting with the $S$ sampled from noisy labeled data. Then train a GCN model with such corrected data. 2) correct noisy labels by soft voting with the $S$ sampled from whole dataset. If for any unlabeled data $x_j\in S$, I exploit the GCN's prediction as its pseudo label. Then retrain the GCN model with such corrected data. This step can be repeated. 

experiment:

The GAE-CGCN is my method, indicating the similarities are measured by GAE and I do label correction with GCN.

The NRGNN is the method from ${[1]}$. We can observe that it fails in all of the cases. This is because I use the standard split of cora and citeseer dataset while NRGNN does not in their paper. In another word, after reading their official code, I find out NRGNN has a lot of tricks, e.g., artificially control random seed as some odd number and did not use the standard dataset split,  which makes the method unrobust.

It is worth to mention that my method also has a hyperparameter $t$, which is the *similarity threshold*. Specifically, if the similarity of any two nodes is larger than $t$, these two nodes should be from the same class.

Currently, this hyperparameter $t$ is set between $[0.7, 0.8]$. I am looking for a method to determine this hyperparameter automatically.

Similarity between NRGNN and my method:

1. NRGNN connetct noisy label with unlabeled data. My method corret noisy label with similar nodes' noisy/pseudo labels. Both of us are rely on feature similarity to find the data belonging to the same class.

Difference (small):

1. My method correct label while NRGNN not. 




[1]《NRGNN: Learning a Label Noise-Resistant Graph Neural Network on Sparsely and Noisily Labeled Graphs》(KDD 2021)