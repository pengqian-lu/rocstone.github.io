---
title: 双硬盘双系统：linux先win后
date: 2021-04-18 21:45:00
tags: Linux
---

装一个linux系统网上的教程非常多，我就不赘述了。难点出在装完linux后如何在另一个硬盘上装win10。即我们想要做到主机上有2块硬盘，硬盘A已经装好linux了，现在想在硬盘B上装Win10。 

中文搜了很久都没有靠谱的答案，用英文google很快就找到了可靠的方案...搞技术的不学点英文真的不行。更坑爹是那种文章写了一大堆，巨长，然后你跟着做，结果崩了，那时候真是气不打一出来。PS：本文适合有装机基础的人看。

下面给出干货，有些基本的操作我就一笔带过了，只讲最重要的内容。

0. 如果你用的是机械键盘，那么最好准备一个薄膜键盘。因为我的机械键盘在**未装任何操作系统**的时候，会无法使用。即无法按任何键进入bios，因为按了键主板也不会有响应。但是换个薄膜键盘就没事了。

0. 硬盘AB都必须是GPT类型，如果不是，用parted。**这一点非常重要**。下面给出代码。

    - `parted /dev/sdb`。我是sdb这块硬盘，你的可能不一样，使用`sudo fdisk -l`，你可以找到自己的硬盘的id。

    - 运行上面的命令后，会进入parted模式，然后再输入`mklabel gpt`就可以把硬盘变成gpt类型了。

1. 硬盘B必须有专门留出一块分区大概300MB，用作UEFI。我当时没留就出问题了。我不知道直接在ubuntu里面分区出300MB可不可以，我当时是把硬盘B连接到另一台笔记本上，然后用diskgenuis里的`建立ESP/MSR分区`功能搞定的。如果没有在你的硬盘B上留出这块空间，在`第五步`安装win10的时候你碰到一个错误，叫`我们无法找到建立新的分区，也无法找到有分区`。

2. [下载win10 iso镜像](https://www.microsoft.com/zh-cn/software-download/windows10ISO)

3. [然后看这里学会在linux下制作win10启动盘](https://10101.io/2019/01/20/create-bootable-windows-usb-in-linux)。未免这位博主删文，以下做转载：
   
   - 安装Woeusb（我并未按照博主的方式安装，因为我电脑里已经有了。）

   - 执行命令 `sudo woeusb --device /path/to/filename.iso  /dev/sdc  --target-filesystem NTFS` 将win10 iso文件写入到u盘。注意！此命令需要梯子，所以要提前在terminal中开启代理，或者用proxychians执行这个命令。即 `sudo proxychains woeusb --device /path/to/filename.iso  /dev/sdc  --target-filesystem NTFS`。

4. 关机，把装有linux的硬盘A的SATA线拔掉，以防linux系统收到任何损失。

5. 开机，设置bios，从usb启动。开始安装win10，把win10装到硬盘B。

    - 如果出现此时按任何键都无法进入bios，那么是时候掏出你的薄膜键盘了。

    - 如果出现：重启后明明插上了u盘但是无法从usb启动

        - 进入bios随便设置点什么，比如调换启动顺序，关掉快速启动等等，然后保存退出。此时会自动重启，然后就能进入u盘的系统了。

    - 如果出现：安装win10时，明明提供

6. 关机，插上装有linux的硬盘A的SATA线。
   
7. 开机，设置bios，从linux启动。进入linux系统，打开terminal

    - `sudo os-prober` 在grub中创建win10启动选项

    - 修改`/etc/default/grub`文件，注释掉`grub-timeout-style=hidden`（也就是这一行前面加个#）。`GRUB_TIMEOUT=10`，grub等待时间设置为10秒。

    - `sudo update-grub` 更新grub设置

8. 此时重新开机，就会发现用grub启动了，而且同时有ubuntu和win10的选项。搞定。


9.  如果你在装win10的时候没有拔掉硬盘A的SATA线，可能出现你bios即使把ubuntu放在第一位了，也无法启动ubuntu，这是因为grub被破坏了，需要制作一个含有ubuntu的u盘启动盘，在里面进行boot repair
    - `sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt-get update`
    
    - `sudo apt-get install -y boot-repair && boot-repair`
    
    - 参考这里https://silentinfotech.com/blog/steps-to-install-windows-10-on-existing-ubuntu-16-04/

其他参考链接

1.  ubuntu+win10，同一个硬盘上先linux后win10 https://www.youtube.com/watch?v=o-2GopsJrIE 

2.  两个硬盘，先linux后win10（本文参考的这个） https://www.youtube.com/watch?v=2ToKTPoAlUk 