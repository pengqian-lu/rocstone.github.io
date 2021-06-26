---
title: >-
  在python代码中设置代理解决问题：pytorch-geometric urllib-error-URLError-urlopen-error-Errno-111-Connection-refused>
date: 2021-04-24 15:10:31
tags: pytorch-geometric
img: /images/gear.jpg
---
安装好pytorch-geometric后直接运行，遇到错误` urllib-error-URLError-urlopen-error-Errno-111-Connection-refused`.

原因出在没有设置好代理

1. 确保你的代理软件已经打开，搞清楚你的http和https的代理端口，以ssr-electron为例，在选项设置中可以找到。
  ![图 1](/images/81035e11d6628cb2afc841b8d4c59d72bb516723d472c56c7dc6b6ec63faa40a.png)  

2. 在python代码的开头加入
   ```python
    import os
    os.environ["HTTP_PROXY"] = 'http://127.0.0.1:12333'
    os.environ["HTTPS_PROXY"] = 'https://127.0.0.1:12333'
   ```