---
title: linux 使用技巧
date: 2021-6-13 13:51:56
tags: Linux
img: /images/gear.jpg
---
## 查看显卡使用情况
查看一次： nvidia-smi
每x秒刷新：nvidia-smi -l x【举例，nvidia-smi -l 2】
每x毫秒刷新：nvidia-smi -lms x

毫秒刷新是有意义的，因为有时候你看上去GPU util已经满载了，但高刷下会发现，其实有时候利用率是0，这就说明其实没有满载，你就可以继续往卡里塞程序。

## 查看进程

一种方式是使用top
![图 1](https://i.loli.net/2021/06/13/24XASgEQTuPjZol.png)  

但是这个太基础了，可以在此界面，按c，显示详细command，这样便于了解各个任务。
![图 2](https://i.loli.net/2021/06/13/GdMI2DbKRsJcT3E.png)  

另外，在top界面按小键盘1，可以查看各个cpu的使用情况
可以看到，此时已经基本满载，这里的us代表的是用户进程的占用比例，基本都80%了

![图 3](https://i.loli.net/2021/06/13/3rXakop8SQdstCe.png)  

top命令一样可以配合grep使用，比如我发现进程太多，我不是很关心，我只想看命令中带有main.py的，因为这部分才是我运行的代码，输入`top c | grep main.py `，注意，此处一定要有个`c`参数，因为直接使用top，command是没有展开的，也就搜索不到main.py。

我个人猜测，这个搜索的原理，是把top输出的内容当做一个文本，然后在里面用正则表达式进行搜索，所以如果top输出的内容里面没有包含main.py的文本，也就搜索不到了，这就是为什么一定要用`top c`，本质上`top c = top 之后再按c`。

![图 6](https://i.loli.net/2021/06/13/xqFJSZ1jW9Mzldp.png)  

## 非常有用：搜索进程并且杀掉进程
经常会出现我们提交了几十个任务，然后发现代码写错了（悲催），需要全部终止，这时候就需要用到根据关键词搜索进程并且杀掉他们的指令。这个指令我现在就不演示了，指令是：`ps -ef | grep main.py | awk '{print $2}' | xargs kill -9`.

awk是啥我也不知道，但是$1代表取前一个管道输出的第一列，查看上面那张图你会发现，第一列正好是pid，所以以后你知道了，如果要取第二列，你就改成 $2。

xargs代表取出前面$1对应的值，作为后面命令的参数，也就是kill的参数。

## 显示各个硬盘的剩余空间

df -h
![图 5](https://i.loli.net/2021/06/13/bnUGq71KtPdOXyk.png)  

## 查看当前目录下所有文件的大小，包括目录和文件
du -ha --max-depth=1

![图 7](https://i.loli.net/2021/06/13/sbrdzK2DWXG9ZhI.png) 

其中h代表文件的单位用人类友好的GB、MB、KB之类的，而不是字节。

a代表显示所有文件，而不仅仅是目录

--max-depth=1代表递归层数是1层，如果没有这个参数，会递归显示文件夹下内所有文件、包括子目录下所有文件的大小，毫无必要。

## ssh连接

linux默认的ssh还是太弱了，复制文件等操作非常不方便，vscode的插件`remote ssh`非常好用，只要目标服务器打开了sftp功能，那么在vscode里面进行ssh连接，你会感觉服务器下的文件，仿佛就在本地一样好用

## 两台机子之间互传文件 or 同步代码

考虑使用国内的github替代品gitee，基本上和GitHub一模一样，但是下载速度可以达到MB+，以此传输文件只需要在终端敲几下键盘就行了，远比拖拽复制粘贴方便的多。

前提是你知道如何使用github。

假如你已经领悟到如何用gitee来传输文件了，那么有一个tip可以介绍给你。因为上传文件需要使用`git add *` + `git commit -m "" + git push origin master`三个命令，一直输入太麻烦了。

打开`~/.bashrc`，在末尾添加一段
`alias gacp=git add *; git commit -m "asd"; git push origin master`.

然后`source ~/.bashrc`来应用变化，最后你只要在终端输入`gacp`就可以一键三连，上传文件，在领一台机子上 `git pull origin master` 就可以了

## 查看多个文件的某一行结果
我把每个程序的pid都print了出来，保存在out文件里，假如我想杀掉某个文件对应的程序，只需要执行`awk 'FNR == 30' *.out`，就可以获得这批out文件的第30行内容。在我的case里，第30行的内容就是pid.

![图 1](https://i.loli.net/2021/06/20/edTm6kAhVNSGxXp.png)  


## 方便的查看git代码

如果想用linux打开一个github仓库，直接修改域名即可，比如要查看https://github.com/DeepGraphLearning/GMNN 的代码，直接输入https://github1s.com/DeepGraphLearning/GMNN 即可。打开结果如下图。
![图 4](https://i.loli.net/2021/06/08/CimpR2Gs79Z3Uxz.png)  


## linux gif 截图
搜索peek即可。

## vscode markdown ppt
vscode markdown 格式编写ppt：搜索vscode插件 Marp for vscode. 当然少不了要了解下他的语法。