---
title: wget设置代理
date: 2021-04-30 20:39:23
tags: Linux
img: /images/gear.jpg
---
首先查看ssr的选项设置，确认自己的http代理端口是多少
我的是12333
然后修改/etc/wgetrc文件，添加三行代码到末尾
```
use_proxy=on
http_proxy=127.0.0.1:8080
https_proxy=127.0.0.1:8080
```