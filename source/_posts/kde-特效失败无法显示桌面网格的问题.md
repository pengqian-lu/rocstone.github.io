---
title: kde 特效失败无法显示桌面网格的问题
date: 2021-05-11 22:28:09
tags: Linux, KDE
img: /images/gear.jpg
---
莫名其妙ctrl+tab没特效了，win+tab也没特效，在快捷键设置里面明明设置了win+tab给显示桌面网格
![图 2](/images/787ccc39e65a1f9df2f5ab4c22b56224eac76585a2f70c9921881233c42fbc27.png)  

但是就是没效果，搜了半天，发现是由于驱动更新，混成器出了问题，在设置-显示和监控-混成器里面，修改渲染后端为xrender可以解决问题

![图 1](/images/790f665705279158a0f78aae4efd9e12e3e0ad1e090a6eaa13ae6b5f57a045e8.png)  

但是xrender会导致某些高级特效不可用，所以能用opengl就用opengl
![图 1](/images/60340c9ea0c0fcb9e36cbff6addcf3819cbb39887f50c4020acb4c324dfbe512.png)  
