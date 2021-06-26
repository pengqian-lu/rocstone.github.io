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