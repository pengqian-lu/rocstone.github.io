---
title: >-
  ERROR: Could not install packages due to an OSError: Missing dependencies for
  SOCKS support.
date: 2021-04-23 20:48:56
tags: Linux
---
这个问题出现在
1. 你在terminal中使用了代理，而其实socks协议的。
2. 你尝试用`pip install`某个包，并且想要想用代理来加速。
3. 结果如上，告诉你不支持socks协议。

解决方案：
改用http协议代理，即
1. `export http_proxy=http://127.0.0.1:12333`
2. `export https_proxy=https://127.0.0.1:12333`

注意上面的端口12333要根据你的代理软件的端口进行修改