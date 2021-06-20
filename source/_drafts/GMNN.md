---
marp: true
theme: gaia
footer: 'Pengqian Lu, 2021.06.08'
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

## **GMNN: Graph Markov Neural Networks**
### Meng Qu, Yoshua Bengio, Jian Tang
#### ICML 2019

---

# Problem to be solved
Semi-supervised object classification in relational data.

**Relational data**: Here, it's graph data.

---

# Related Field: SRL 
**Statistical relational learning (SRL)** develops statistical methods to model relational data. Generally theses methods model the dependency of object labels using conditional random fields (CRF).

---
## Brief introduction of CRF

<!-- ![图 3](https://i.loli.net/2021/06/07/VyPvXTWOfes32Kx.png)   -->

Given a graph $G=(V,E,X,Y)$, let $V$ denotes a set of nodes, $E$ denotes a set of edges, $X$ denotes the feature matrix on nodes and $Y$ denotes the labels of nodes.

---
## Brief introduction of CRF
CRF models the conditional probability of $Y$ given $X$ as:

$p(Y|X)=\frac{1}{Z(X)}\prod_{(n_i,n_j)\in E}-\exp\{\psi_{ij}(y_i,y_j,X)\}$

Here, $Z=\sum_Y\prod_{(n_i,n_j)\in E}-\exp\{\psi_{ij}(y_i,y_j,X)\}$

Potential functions $\psi_{i,j}$, e.g.,

 $\psi_{ij}=A_i(y_i,X)+I_{ij}(y_i,y_j,X)=\alpha |x_i-y_i| + \beta |y_i - y_j|$

We want to learn $p_\phi(Y|X)$ where $\phi$ is all of the parameters of potential functions such as $\alpha$ and $\beta$.

<!-- Scoped style -->
<style scoped>
p {
  font-size: 28px;
}
</style>


---
## Brief introduction of CRF

The limitations of SRL methods:
1. the potential functions in CRF are linear combinations of some hand-crafted feature functions. The capacity of such models is insufficient.
2. Inferring the posterior distributions of object labels for unlabeled objects remains a challenging problem.

---

## Brief introduction of GNN
<!-- ![图 4](https://i.loli.net/2021/06/07/lIgqVrtESc9sAGy.png)   -->

Learning object representations with non-linear neural architectures.

---
## Brief introduction of GNN
The limitation of such methods:

1. Labels of objects are independently predicted on their representations. But the joint dependency of object labels is ignored.

**Motivation of GMNN**: 

Combine the advantages of both the CRF and GNN: learning objective representations for predicting object labels and modeling the dependency between object labels.

---

# Brief introduction of GMNN

1. GMNN models the joint distribution of object labels conditioned on object attributes, i.e. $p(Y|X)$, by using CRF.

2. Optimize it with a pseudolikelihood variational EM framework. 
   
    - In the E-step, a GNN is used to learn object representations for label prediction. [as GCN]
    - In the M-step, another graph neural network is employed to model the local dependency of object labels. [as CRF]
     
---
# Pseudolikelihood Variational EM
1. We want to learn $p_\phi(Y|X)$. Assuming the potential functions of it is known, we only have to learn parameter $\phi$.

2. But $Y=\{Y_L,Y_U\}$ while $Y_U$ is unknown.

3. Since $p_\phi(Y|X)$ and $p_\phi(Y_L|X)$ have the same parameter $\phi$, we try to learn it from $p_\phi(Y_L|X)$.


---
# Pseudolikelihood Variational EM
With evidence lower bound (ELBO), we know:

$\log p_\phi(Y_L|X) ＝ \mathbb{E}_{q_\theta(Y_U|X)}[\log p_\phi(Y_L,Y_U|X) - \log q_\theta(Y_U|X)]$
　　　　　　　$+ KL(q_\theta(Y_U|X)||p_\phi(Y_U|Y_L,X))$

where $q_\theta(Y_U|X)$ is a any distribution over $Y_U$.

This equation corresponds to `Evidence = ELBO + Gap`.

Since $q_\theta$ is independent from $p_\phi$, optimizing the ELBO means minimizing Gap which means $q_\theta(Y_U|X)$ approachs $p_\phi(Y_U|Y_L,X)$.

---
# Pseudolikelihood Variational EM

Now we try to optimize ELBO, i.e., $\argmax_\theta \mathbb{E}_{q_\theta(Y_U|X)}[\log p_\phi(Y_L,Y_U|X) - \log q_\theta(Y_U|X)]$. 

But it is not easy since we still don't know $p_\phi$. 

So, we want to find a method that can learn $p_\phi$ and $q_\theta$ simutaneously.

The answer is EM algorithm.

---
# Pseudolikelihood Variational EM

EM algorithm is used to optimized such ELBO alternatively.
   - E-step, fix $p_\phi$ and update $q_\theta(Y_U|X)$ to approach $p_\phi(Y_U|Y_L,X)$. 
       - recall this minizes the Gap
   - M-step, fix $q_\theta$ and maximize the ELBO to learn $p_\phi$. 
       - when gap is 0, Evidence=ELBO, maximizing ELBO means MLE

---

# Pseudolikelihood Variational EM
However, ELBO = $\mathbb{E}_{q_\theta(Y_U|X)}[\log p_\phi(Y_L,Y_U|X)]$.

The parition function in $p_\phi$ is difficult to deal with.

Instead, we try to optimize the pseudolikelihood function which is an approximation to the joint probability distribution (widely used in Markov networks).

---
# Pseudolikelihood Variational EM
**Pseudolikelihood**
ELBO = $\mathbb{E}_{q_\theta(Y_U|X)}[\log p_\phi(Y_L,Y_U|X)]\simeq \ell_{PL}(\phi)=\mathbb{E}_{q_\theta(Y_U|X)}[\sum_i \log p_\phi(y_i|Y_{V\backslash i},X)]=\mathbb{E}_{q_\theta(Y_U|X)}[\sum_i \log p_\phi(y_i|Y_{n(i)},X)]$

where $n(i)$ denotes the neighbors of node $v_i$.

---
# Pseudolikelihood Variational EM

EM algorithm is used to optimized such ELBO alternatively.
   - E-step, fix $p_\phi$ and update $q_\theta(Y_U|X)$ to approach $p_\phi(Y_U|Y_L,X)$. 
   - M-step, fix $q_\theta$ and maximize the $\ell_{PL}(\phi)$ to learn $p_\phi$. 
  
---
# E-step

<!-- Scoped style -->
<style scoped>
p {
  font-size: 28px;
}
</style>

At E-step, we try to learn $q_\theta(Y_U|X)$. But jointly learning it is not easy. 

So we assume that labels are independently (strong assumption).

i.e., $q_\theta(Y_U|X)=\prod_{y_i\in Y_U} q_\theta(y_i|X)$

Then we can model it by GNN as $q_\theta(y_i|X)=\mathrm{softmax}(W_\theta h_{\theta,i})$

The representation $h_{\theta,i}$ is learned by GNN with $X$ as feature.

However, we $Y_U$ is unobserved which means we can't learn $q_\theta(y_i|X)$ with supervised learning.


---
# E-step

<!-- Scoped style -->
<style scoped>
p {
  font-size: 28px;
}
</style>
According to a long proof of the paper, it shows that
$\log q^*(y_i|X)=\mathbb{E}_{q_\theta(Y_N(i)\bigcap U|X)}[\log p_\phi(y_i|Y_{N(i)},X)]+\mathrm{const}$

$\simeq \log p_\phi(y_i|\hat{Y}_{N(i)},X)$

Here, $\hat{Y}_{N(i)}=\{\hat{y}_{i'}\}_{i'\in N(i)}$.

If $v_i$ is a unlabeled node: $\hat{y}_{i'}\sim q_\theta(y_{i'}|X)$

If $v_i$ is a label node: $\hat{y}_{i'}=y_i$

Now, we find a way to calculate $q_\theta$: set it as $p_\phi(y_i|\hat{Y}_{N(i)},X)$.

---
# E-step
<!-- Scoped style -->
<style scoped>
p {
  font-size: 28px;
}
</style>
How to set $\log q^*(y_i|X)=p_\phi(y_i|\hat{Y}_{N(i)},X)$?

Minimizing $\mathrm{KL}(p_\phi(y_i|\hat{Y}_{N(i)},X)||\log q^*(y_i|X))$

Thus, we have objective $O_{\theta,U}=\sum_{i\in U}\mathbb{E}_{p_\phi(y_i|\hat{Y}_{N(i)},X)}[\log q(y_i|X)]$

For labelled nodes, we can simply use supervised learning:

$O_{\theta,L}=\sum_{i\in L}\log q_\theta(y_i|X)$

The overall objective of E-step is $O_\theta=O_{\theta,U}+O_{\theta,L}$.

---
# M-step
M-step: fix $q_\theta$ and learning $p_\phi$ with maximizing $\ell_{PL}$.

i.e., $\mathbb{E}_{q_\theta(Y_U|X)}[\sum_i \log p_\phi(y_i|Y_{n(i)},X)]$

Again, we use another $\mathrm{GNN}_\phi$ to model $p_\phi$,

i.e., $p_\phi(y_i|Y_{n(i)},X)=\mathrm{softmax}(W_\phi h_{\phi,i})$.

$h_{\phi,i}$ is the representation learned by $\mathrm{GNN}_\phi$ with features $X$ and labels of neighbors $Y_{N(i)}$ as input.

---
# M-step
Similarly, we estimate the objective $O_\phi=\mathbb{E}_{q_\theta(Y_U|X)}[\sum_i \log p_\phi(y_i|Y_{n(i)},X)]\sim \sum_i \log p_\phi(\hat{y}_i|\hat{Y}_{n(i)},X)$

where $\hat{y}_i\sim q_\theta(y_n|X)$ if $y_i$ is unlabeled, otherwise $\hat{y_i}=y_i$.

---
# Overall Algorithm
1. Pretrain $q_\theta$ with $Y_L$ according to $O_{\theta,L}=\sum_{i\in L}\log q_\theta(y_i|X)$.
2. While not converge:
    - M-step: annotate unlabeled nodes with $q_\theta$ and learn $p_\phi$ with $O_\phi$
    - E-step: annotate unlabeled nodes with $p_\phi$ and learn $q_\theta$ with $O_\theta$.
3. classify each unlabeled node with $q_\theta(y_i|X)$.

---
# For unsupervised learing
The only problem is we need some labeled data to pretrain $q_\theta$. The solution is initilizing each node with different label and do label propagation to create pseudo labels.

Then train $p_\phi$ and $q_\theta$ with EM algorithm as introduced before.

---
# For link classification
The main idea is building a new graph from the original graph. The nodes of the new graph corresponding to the link of the original graph.

Two nodes in the new graph is connected if they share the same node in the orginal graph.

The feature of each node in the new graph is the features of the nodes corresponding to the link in the original graph.

---
# Result

![图 2](https://i.loli.net/2021/06/08/5dMHOztjRkXnPfC.png)  

`Idea.` Using node attribute is not necessary better than not. Which means an algorithm reweight the importance of node features and label features can be promising.


