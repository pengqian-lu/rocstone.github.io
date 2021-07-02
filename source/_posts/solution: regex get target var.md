---
title: 正则表达式批量替换目标两边的符号
date: 2021-04-22 20:59:11
tags: 正则表达式
img: /images/gear.jpg
---
现有这样一段代码
```python
opt['dataset'] = '../data/cora'
opt['hidden_dim'] = 16
opt['input_dropout'] = 0.5
opt['dropout'] = 0
opt['optimizer'] = 'rmsprop'
opt['lr'] = 0.05
opt['decay'] = 5e-4
opt['self_link_weight'] = 1.0
opt['pre_epoch'] = 100
opt['epoch'] = 100
opt['iter'] = 1
opt['use_gold'] = 1
opt['draw'] = 'smp'
opt['tau'] = 0.1
opt['noise_rate'] = -1
opt['type'] = 'symmetric'
```
我想把opt修改成args，把引用字典的形式改成引用对象成员变量的形式。所以等于每一行的键值保持边，opt['与']做替换。

做法：

用正则表达式选择所有目标`opt\['(.*)']`

将选择目标替换为`args.$1`

这里的`$1`就能提取到`(.*)`里面的内容。

这个正则表达式选择+替换可以用在任何支持正则表达的场景，比如pycharm。

![图 1](/images/cb377345dfa75bb1c350cc405776c92ef8ff43be392d24778dbf74324199d228.png)  

替换后变成

![图 2](/images/10279d619d829c383dfcf8be1f8ef5b266f08a229185086b08b42c8b39302d99.png)  
