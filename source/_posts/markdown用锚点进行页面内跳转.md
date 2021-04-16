---
title: github readme中的markdown用锚点进行页面内跳转
date: 2021-04-16 20:24:51
tags: markdown
---

两步搞定

---

在跳转目标旁边包裹上如下html代码，注意href的值只能用英文设置。

`<span class='anchor' href='hello'>大家好！</span>`

然后在跳转点包裹上如下代码

`[点我就跳转](#hello)`

---
举个例子，[点我就跳转](#hello)
</br></br></br></br></br></br>
<span class='anchor' href='hello'>大家好！</span>