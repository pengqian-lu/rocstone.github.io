---
title: hexo建立博客要注意travis ci自动部署无法触发的问题
date: 2021-04-16 17:56:47
tags: Hexo
---
[根据这篇非常好的博客](https://mfrank2016.github.io/breeze-blog/2020/05/02/hexo/hexo-start/#toc-heading-3)，我成功部署了hexo。基本上直接照做就行，但是会出现问题（必然的），所以在这里先打个预防针

## 稍微解释一下travis

你的电脑上的hexo工程是源代码，你用hexo g -d会生成静态博客文件，并且上传到github上。

但是保存在github上的是你的静态博客文件，你的源代码在你的电脑上，假如你换一台电脑，你就没办法更新博客了。

所以一个想法是把源代码也上传到github上去，把静态博客也上传到github上，然后你换了机子也没事，直接一起git clone 就行。

现在的问题是好事者觉得这样太麻烦，因为你又要上传源代码，又要上传静态博客文件，能不能只做一步，把源代码上传上去，静态文件让他自动生成？能！用travis。这就被称为自动部署了。

## 两个坑
  
  1. 现在github你如果不修改，在创建repo的时候，主分支的名字是main，跟这个博客中的大量的内容不一样，所以建议还是把主分支改成master吧。具体来说。在创建repo时，如果你下方显示的不是master而是main，那么点击setting设置一下，把默认分支名字改成master，反正我们中国人也没这种政治正确，没必要避讳master。

      ![picture 1](source/images/b0aa3bcbbf199386fb37963c0fb7fd62ca18547472896a4c0737732184b4bbb9.png)  

  2. 在大佬博客的第七步中，执行的是“使用 travis ci 进行自动化部署”。在master分支下添加完`.travis.yml`文件后，一定要记得`git add .travis.yml`专门添加此文件。在我的机子上直接`git add *`是会跳过这个文件的。

祝你好运！
![图 1](../images/7fd8be1f0a7d8c5f0a703318a6b77d5e5219e17e9830f9c6b54d85d9e972e9d5.png)  
