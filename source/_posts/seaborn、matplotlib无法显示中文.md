---
title: seaborn、matplotlib无法显示中文
date: 2021-05-22 19:58:22
tags: 可视化, matplotlib, seaborn
---

Linux 下解决方案参考[此处](https://www.shangmayuan.com/a/e32340a6b72f479a838fdba1.html)。

此文并不完全正确，不利于新手，所以重新阐述如下。

1. 通过代码
```python
import matplotlib
matplotlib.matplotlib_fname()
```
可以得知`matplotlibrc`文件所在位置，比如我在
`/home/roc/anaconda3/envs/zhihu/lib/python3.8/site-packages/matplotlib/mpl-data/matplotlibrc`这个路径下。这个路径非常重要，一会用到。

2. 从[此处](https://www.fontpalace.com/font-details/SimHei/)下载`simhei.ttf`字体，虽然本机是linux，但是这个显示只能给win和mac的字体也能用。

3. 把`simhei.ttf`放到路径`~/home/roc/anaconda3/envs/zhihu/lib/python3.8/site-packages/matplotlib/mpl-data/fonts/ttf`下。

4. 修改`matplotlibrc`文件，取消注释这两行
```
font.family         : sans-serif
#下面这行，取消注释后添加新加入的simhei字体名称
font.sans-serif     : simhei, Bitstream Vera Sans, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif
```

5. 将该`matplotlibrc`文件文件拷贝到`~/.cache/matplotlib`目录下，并删除`~/.cache/matplotlib` 下的`fontList.josn文件`（从新启动python文件后会从新生成）。注意，我这边叫`fontlist-v330.json`，所以根据不同情况名字可能会不同。

6. 在你的seaborn或者matplotlib代码中加入
```
import matplotlib as mpl
import matplotlib.pyplot as plt  
mpl.rcParams['font.sans-serif'] = ['simhei']
mpl.rcParams['font.serif'] = ['simhei']
import seaborn as sns
sns.set_style("darkgrid",{"font.sans-serif":['simhei','Droid Sans Fallback']})
```
将字体切换成黑体simhei，就能正常显示中文了。

