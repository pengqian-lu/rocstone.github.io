---
marp: true
theme: gaia
footer: 'Pengqian Lu, 2021.07.03'
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

# Idea 1: MIsup for GNN

## Problem to be solved
Semi-supervised node classification on graph data with noisy labels.

1. Mixup can reduces the memorization of corrupt labes. `mixup: BEYOND EMPIRICAL RISK MINIMIZATION`

2. GNNs such as GCN are similar with mixup: both of them mixup object features. 

    - in GCN, representation of $l+1$-th layer: $H^{(l+1)}=\sigma(\tilde{D}^{-1/2}\tilde{A}\tilde{D}^{-1/2}H^{(l)}W^{(l)})$
    - when $l=0, H^{(l)}=X$
  
3. But GCN doesn't mixup labels

    - loss of GCN is; $\ell=\mathrm{CE}(\mathrm{Softmax}(H^2), Y)$

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

1. We use a "MixNet" to mixup labels.

    - mixed labels at $l+1$-th layer: $H_{Y}^{(l+1)}=\tilde{D}^{-1/2}\tilde{A}\tilde{D}^{-1/2}H_Y^{(l)}$

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

# Idea 2: not working


1. Mutual information measure the  correlation between two variables. Since neighbors in graph is dependent on each other, their mutual information should be high.

2. The corrupted labels breaks the dependence which should brings small MI between them and their neighbors. Thus, it can be exploited to find noisy labels.

3. However, it is difficult to directly estimate MI between two labels. 


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


# Idea 3: label correction with MI maximization.



1. pretrain GCN with labeled data, use the output of GCN as pseudo labels.

2. training GCN via maximizing MI between adjacent pesudo labels

    - as MI between pesudo labels grows, pesudo labels are gradually dependent on each other which should be more reliable than given labels.

3. output GCN or train GCN with pesudo labels like `seudo-Label : The Simple and Efficient Semi-Supervised Learning Method for Deep Neural Networks`
