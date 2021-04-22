---
title: Pytorch下载mnist等数据集无法使用代理
date: 2021-04-22 16:28:45
tags: Pytorch
---
明明有ssr代理，下载数据却还是很慢，原因出在下载代码并没有启用ssr

解决办法：在所有代码之前添加代码以使用代理
```python
from six.moves import urllib

proxy = urllib.request.ProxyHandler({'http': '127.0.0.1:12333'})
# construct a new opener using your proxy settings
opener = urllib.request.build_opener(proxy)
# install the openen on the module-level
urllib.request.install_opener(opener)
```

我的ssr的http代理端口是12333，注意改成你的对应的端口。

来源：https://github.com/pytorch/vision/issues/1033#issuecomment-503007804