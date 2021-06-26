---
title: 安装pytorch_geometric报错AttributeError NoneType object has no attribute origin
date: 2021-04-23 22:08:14
tags: pytorch-geometric
img: /images/gear.jpg
---

我首先按照[官网](https://pytorch-geometric.readthedocs.io/en/latest/notes/installation.html)的第一种方式安装pytorch_gemometric.

结果并没有成功，报错`AttributeError: NoneType object has no attribute origin`

我参照[这个人](https://github.com/rusty1s/pytorch_geometric/issues/2429#issuecomment-825046046)的建议，首先添加cuda到各种PATH，然后卸载对应的所有包
```
pip uninstall torch-scatter
pip uninstall torch-sparse
pip uninstall torch-cluster
pip uninstall torch-spline-conv
pip uninstall torch-geometric
```
最重要的是，不要用cache，重新安装
```
pip install torch-scatter --no-cache-dir
pip install torch-sparse --no-cache-dir
pip install torch-cluster --no-cache-dir
pip install torch-spline-conv --no-cache-dir
pip install torch-geometric 
```
问题解决