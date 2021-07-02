---
marp: true
theme: gaia
footer: 'Pengqian Lu, 2021.07.02'
paginate: true
style: |
  section a {
      font-size: 30px;
  }
---

<!--
_class: lead gaia
_paginate: false
-->

## **Ideas about label noise in GNN**

---
<!-- Scoped style -->
<style scoped>
p {
  font-size: 26px;
}
{
  font-size: 26px;
}

</style>

# Idea 1

## Problem to be solved
Semi-supervised node classification on graph data with noisy labels.

1. Mixup can reduces the memorization of corrupt labes. `mixup: BEYOND EMPIRICAL RISK MINIMIZATION`

2. GNNs such as GCN are similar with mixup: both of them mixup object features. 

    - in GCN, representation of $l+1$-th layer: $H^{(l+1)}=\sigma(\tilde{D}^{-1/2}\tilde{A}\tilde{D}^{-1/2}H^{(l)}W^{(l)})$
    - when $l=0, H^{(l)}=X$
  
3. But GCN doesn't mixup labels
    - $\ell=\mathrm{CE}(\mathrm{Softmax}(H^2), \mathrm{Softmax}(H_Y^2))$

4. Thus, we try to apply mixup to GNNs by mixing up labels.

---
<!-- Scoped style -->
<style scoped>
p {
  font-size: 26px;
}
{
  font-size: 26px;
}

</style>

# Mixup labels

1. We use another MixNet to mixup labels with parameters set as identity matrix.

    - mixed labels at $l=1$-th layer: $H_{Y}^{(l+1)}=\tilde{D}^{-1/2}\tilde{A}\tilde{D}^{-1/2}H_Y^{(l)}$

    - $H_Y^0=Y$

2. The loss of  GCN-based Mixup method is 
    - $\ell=\mathrm{CE}(\mathrm{Softmax}(H^2), \mathrm{Softmax(H^2_Y)})$

## Another problem is we only know a part of $Y$

Solution: pretrain a GNN with labeled data and make predictions on all data. 

Use the prediction as pseudo label.

---
<!-- Scoped style -->
<style scoped>
p {
  font-size: 26px;
}
{
  font-size: 26px;
}

</style>

# Idea 2


1. 在labeled data上pretrain一个GCN。

2. 以maximize mutual information between label prediciton of neighbors 作为目标函数，来训练神经网络。以此为目标可以correct label。

3. 以prediciton作为pseudo label来训练GCN，参考`seudo-Label : The Simple and Efficient Semi-Supervised Learning Method for Deep Neural Networks`



---