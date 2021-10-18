急死！CPU被挖矿了，却找不到哪个进程！ - 简书

[](https://www.jianshu.com/)

[登录](https://www.jianshu.com/sign_in)[注册](https://www.jianshu.com/sign_up)[写文章](https://www.jianshu.com/writer)

[首页](https://www.jianshu.com/)[下载APP](https://www.jianshu.com/apps?utm_medium=desktop&utm_source=navbar-apps)

# 急死！CPU被挖矿了，却找不到哪个进程！

[![](https://upload.jianshu.io/users/upload_avatars/23633704/44b1ed66-2bbe-4790-ada0-40076482bf77?imageMogr2/auto-orient/strip|imageView2/1/w/80/h/80)码不动](https://www.jianshu.com/u/bcef08d5551e)

# 急死！CPU被挖矿了，却找不到哪个进程！

[![](https://upload.jianshu.io/users/upload_avatars/23633704/44b1ed66-2bbe-4790-ada0-40076482bf77?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96)](https://www.jianshu.com/u/bcef08d5551e)

[码不动](https://www.jianshu.com/u/bcef08d5551e)[<img width="16" height="16" src=":/ea42e96a675140739b11c8b0e270aa0b"/>](https://www.jianshu.com/mobile/creator)

62021.01.07 16:06:20字数 1,670阅读 2,834

## CPU起飞了

最近有朋友在群里反馈，自己服务器的CPU一直处于高占用状态，但用**top**、**ps**等命令却一直找不到是哪个进程在占用，怀疑中了**挖矿病毒**，急的团团转。

![](https://upload-images.jianshu.io/upload_images/23633704-98c21d45ea86b8f2?imageMogr2/auto-orient/strip|imageView2/2/w/581)

图片

![](https://upload-images.jianshu.io/upload_images/23633704-81fb17312dac924c?imageMogr2/auto-orient/strip|imageView2/2/w/572)

图片

根据经验，我赶紧让他看一下当前服务器的网络连接，看看有没有可疑连接，果然发现了有点东西：

![](https://upload-images.jianshu.io/upload_images/23633704-2f8c3545e2ed1985?imageMogr2/auto-orient/strip|imageView2/2/w/571)

图片

上**Shodan**查一下这IP地址：

![](https://upload-images.jianshu.io/upload_images/23633704-2b7735e8434eca65?imageMogr2/auto-orient/strip|imageView2/2/w/1080)

图片

反向查找，发现有诸多域名曾经解析到这个IP地址：

![](https://upload-images.jianshu.io/upload_images/23633704-91364d5cc84f0a31?imageMogr2/auto-orient/strip|imageView2/2/w/634)

图片

这是一个位于德国的IP地址，开放了`4444`,`5555`,`7777`等数个特殊的服务端口：

![](https://upload-images.jianshu.io/upload_images/23633704-081874a9dda25f9c?imageMogr2/auto-orient/strip|imageView2/2/w/688)

图片

其中这位朋友服务器上发现的连接到的是7777端口，**钟馗之眼**显示，这是一个HTTP服务的端口，直接访问返回的信息如下：

![](https://upload-images.jianshu.io/upload_images/23633704-2caf6b96130b64fc?imageMogr2/auto-orient/strip|imageView2/2/w/550)

图片

**mining pool!**，服务器正在挖矿实锤了！

![](https://upload-images.jianshu.io/upload_images/23633704-032b05c05901e833?imageMogr2/auto-orient/strip|imageView2/2/w/1080)

图片

但神奇的是，这个进程像是隐身了一般，找不到存在的任何痕迹。

## 进程如何隐藏

现在说回到本文的正题：**Linux操作系统上，进程要隐藏起来，有哪些招数？**

要回答这个问题，先来知道ps、top等命令枚举系统的进程列表的原理。

Linux的设计哲学是：**一切皆文件！**

进程也不例外， Linux系统中有一个特殊的目录：**/proc/**，这个目录下的内容，不是硬盘上的文件系统，而是操作系统内核暴露出的内核中进程、线程相关的数据接口，也就是**procfs**，里面记录了系统上正在运行的进程和线程信息，来查看一下：

![](https://upload-images.jianshu.io/upload_images/23633704-5df9bcdadd65f8c5?imageMogr2/auto-orient/strip|imageView2/2/w/1080)

图片

这些以数字命名的目录，就是一个进程的PID，里面记录了该进程的详细信息。

而ps、top等命令的工作原理，实质上就是遍历这个目录。

知道了原理，想实现隐藏就有以下几个思路：

### 命令替换

直接替换系统中的ps、top命令工具。可以从GitHub上下载它们的源码，加入对应的过滤逻辑，在遍历进程的时候，剔除挖矿进程，实现隐藏的目的。

### 模块注入

编写一个动态链接库so文件，在so中，HOOK遍历相关的函数（**readdir/readdir64**），遍历的时候，过滤挖矿进程。

通过修改**LD_PRELOAD**环境变量或/etc/ld.so.preload文件，配置动态链接库，实现将其注入到目标进程中。

### 内核级隐藏

模块注入的方式是在应用层执行函数HOOK，隐藏挖矿进程，更进一步，可以通过加载驱动程序的方式在内核空间HOOK相应的系统调用来实现隐藏。不过这对攻击者的技术要求也更高，遇到这样的病毒清理起来挑战也更大了。

## 揪出挖矿进程

通过上面的进程隐藏原理看得住来，都是想尽办法隐藏/proc目录下的内容，类似于“**障眼法**”，所以包含**ps**、**top**、**ls**等等在内的命令，都没办法看到挖矿进程的存在。

但蒙上眼不代表不存在，有一个叫**unhide**的工具，就能用来查看隐藏进程。

我让这位朋友安装这个工具来查找隐藏的进程，但奇怪的是，一执行**yum install**安装，远程连接的SSH会话就立刻断开。

于是退而求其次，选择通过源码安装，又是一直各种报错···

因为我没办法亲自操作这台服务器，沟通起来比较麻烦，于是我决定研究下这个unhide工具的源码，然后编一个python脚本发给他执行。

源码地址：`https://github.com/YJesus/Unhide-NG/blob/master/unhide-linux.c`

在查找隐藏进程模块，其大致使用了如下的方法：

> 挨个访问 **/proc/pid/** 目录，其中，pid从1到到max_pid累加
> 
> - 如果目录不存在，跳过
> - 如果是unhide自己的进程，跳过
> - 如果在ps命令中能看到，跳过
> - 剩下的，既不是自己，也不在ps命令输出中，则判定为隐藏进程

按照这个思路，我编写了一个Python脚本发给这位朋友，执行后果然发现了隐藏的进程：

![](https://upload-images.jianshu.io/upload_images/23633704-d0092cad90df4a7b?imageMogr2/auto-orient/strip|imageView2/2/w/357)

图片

别着急，不是真的有这么多进程，这里是把所有的线程ID列举出来了。随便挑选了一个看一下：

![](https://upload-images.jianshu.io/upload_images/23633704-95f3d9c6a01dd0aa?imageMogr2/auto-orient/strip|imageView2/2/w/495)

图片

还记得前面通过**netstat**命令看到挖矿进程建立了一个网络连接吗？Linux一切皆文件，在 **/proc/pid/fd** 目录下有进程打开的文件信息：

![](https://upload-images.jianshu.io/upload_images/23633704-11b12a26c571192c?imageMogr2/auto-orient/strip|imageView2/2/w/486)

图片

这里发现这个进程打开了一个socket，后面的10212是inode id，再通过下面的命令看一下这个socket到底是什么：

> cat /proc/net/tcp | grep 10212

输出了四元组信息：

![](https://upload-images.jianshu.io/upload_images/23633704-9350e03aef2e33c0?imageMogr2/auto-orient/strip|imageView2/2/w/946)

图片

左边是源IP地址：源端口，右边是目的IP地址：目的端口

目的端口1E61就是7777！！！

![](https://upload-images.jianshu.io/upload_images/23633704-3c189ceafac870f5?imageMogr2/auto-orient/strip|imageView2/2/w/151)

图片

找到了，就是这货！

再次查看 **cat /proc/pid/environ**，定位到进程的可执行文件：

![](https://upload-images.jianshu.io/upload_images/23633704-90b460ea244c7fb9?imageMogr2/auto-orient/strip|imageView2/2/w/568)

图片

总算把这家伙找到了：

![](https://upload-images.jianshu.io/upload_images/23633704-1542bed7ed485c24?imageMogr2/auto-orient/strip|imageView2/2/w/459)

图片

网上一搜这家伙，看来是惯犯了：

![](https://upload-images.jianshu.io/upload_images/23633704-2f899432bb3c7e3f?imageMogr2/auto-orient/strip|imageView2/2/w/1080)

图片

## 挖矿病毒分析

把这个挖矿木马下载下来，反汇编引擎中查看，发现加壳了。

脱壳后，在IDA中现出了原形，不禁倒吸了一口凉气，居然悄悄修改`/root/.ssh/authorized_keys`文件，添加了RSA密钥登录方式，留下这么一个后门，随时都能远程登录进来。

![](https://upload-images.jianshu.io/upload_images/23633704-639e66ad136dea4e?imageMogr2/auto-orient/strip|imageView2/2/w/864)

图片

![](https://upload-images.jianshu.io/upload_images/23633704-db768ecc6b8e7532?imageMogr2/auto-orient/strip|imageView2/2/w/1056)

图片

除此之外，还发现了病毒尝试连接的大量域名：

![](https://upload-images.jianshu.io/upload_images/23633704-424c6432aa5cb371?imageMogr2/auto-orient/strip|imageView2/2/w/404)

图片

看到这里简直可怕！自己的服务器被病毒按在地上摩擦啊！

![](https://upload-images.jianshu.io/upload_images/23633704-ce1e9288b619b94e?imageMogr2/auto-orient/strip|imageView2/2/w/317)

图片

## 清除建议

> - 开启SELinux
> - 杀掉挖矿进程
> - 删除病毒程序（注意rm命令是否被替换）
> - 删除病毒驱动程序（注意rm命令是否被替换）
> - 删除病毒添加的登录凭据
> - 防火墙封禁IP、端口

这个病毒到底是怎么植入进来的呢？？？

咱们下回分解～

74人点赞

[随笔](https://www.jianshu.com/nb/46152039)

"小礼物走一走，来简书关注我"

还没有人赞赏，支持一下

[![  ](https://upload.jianshu.io/users/upload_avatars/23633704/44b1ed66-2bbe-4790-ada0-40076482bf77?imageMogr2/auto-orient/strip|imageView2/1/w/100/h/100)](https://www.jianshu.com/u/bcef08d5551e)

[码不动](https://www.jianshu.com/u/bcef08d5551e "码不动")[<img width="16" height="16" src=":/ea42e96a675140739b11c8b0e270aa0b"/>](https://www.jianshu.com/mobile/creator)欢迎关注我的公众号“互联网Java高级架构”，每天推送各种Java相关干货文章。 欢迎关注我...

总资产123 (约10.72元)共写了9.0W字获得3,081个赞共7,831个粉丝

### 被以下专题收入，发现更多相似内容

[![](https://upload.jianshu.io/collections/images/16/computer_guy.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)程序员](https://www.jianshu.com/c/NEt52a)[![](https://upload.jianshu.io/collections/images/1781619/test.png?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)linux](https://www.jianshu.com/c/9c6c4cb7f870)[![](https://upload.jianshu.io/collections/images/1837847/crop1574386711350.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)linux运维](https://www.jianshu.com/c/2bfabbe57ab7)[![](https://upload.jianshu.io/collections/images/1903773/1581564312.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)数字货币](https://www.jianshu.com/c/cb135adffc14)[![](https://upload.jianshu.io/collections/images/1931871/%E4%B8%8B%E8%BD%BD.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)linux部署](https://www.jianshu.com/c/c0a95f9e3e4d)[![](https://upload.jianshu.io/collections/images/1904205/Snipaste_2020-08-12_05-36-48.png?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)Linux](https://www.jianshu.com/c/c757a226529e)[![](https://upload.jianshu.io/collections/images/259026/android.graphics.Bitmap_bf88bf8.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)Java 杂谈](https://www.jianshu.com/c/0b39448c4e08)

展开更多

### 推荐阅读[更多精彩内容](https://www.jianshu.com/)

- [程序员养家活口接私活必备网站（顺便用技术改变世界）](https://www.jianshu.com/p/6c60a115d459)
    
    程序员接私活的原因很多种(挣钱、养家糊口、提升技术等等)。下面整理了一下网站送给最有潜能的你。 提前准备好自己的笔...
    
    [![](https://upload.jianshu.io/users/upload_avatars/23082890/8d364f28-a6c7-4a8a-9d0a-588317ac966f.png?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)程序员的青春](https://www.jianshu.com/u/fe488e7874b3)阅读 9,499评论 9赞 244
    
    [![](https://upload-images.jianshu.io/upload_images/23082890-15d62c529fc38df4.png?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/6c60a115d459)
- [CPU深夜狂飙，一帮大佬都傻眼了···](https://www.jianshu.com/p/87ce68417cdb)
    
    傍晚时分，警报声乍起，整个Linux帝国都陷入了惊恐之中。 安全部长迅速召集大家商讨应对之策。 “诸位，突发情况，...
    
    [![](https://upload.jianshu.io/users/upload_avatars/23633704/44b1ed66-2bbe-4790-ada0-40076482bf77?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)码不动](https://www.jianshu.com/u/bcef08d5551e)阅读 3,008评论 8赞 79
    
    [![](https://upload-images.jianshu.io/upload_images/23633704-21cd4d13acae0bb2?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/87ce68417cdb)
- [推荐一款最强 Python 自动化神器！不用写一行代码！](https://www.jianshu.com/p/0f83f8b64648)
    
    此文章来源于官方公众号：「测试开发技术」版权声明：允许转载，但转载必须保留原链接；请勿用作商业或者非法用途 搞过自...
    
    [![](https://upload.jianshu.io/users/upload_avatars/19799745/20c04640-d55d-4b90-8d55-8fd58d84aa5c.png?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)测试开发技术](https://www.jianshu.com/u/39194b45e69e)阅读 3,215评论 3赞 64
    
    [![](https://upload-images.jianshu.io/upload_images/19799745-9bbbbccba52bb281.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/0f83f8b64648)
- [是时候跟Docker说再见了](https://www.jianshu.com/p/f14a41e8cfbe)
    
    在容器的远古时代(差不多就是 4 年前)，Docker 是这场游戏的唯一玩家。但现在情况已经不一样了，Docker...
    
    [![](https://upload.jianshu.io/users/upload_avatars/18410372/999ed150-20e6-4382-a1bf-0ccdda97ed2b.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)TASKCTL](https://www.jianshu.com/u/6478d8f5fec8)阅读 3,404评论 5赞 18
    
    [![](https://upload-images.jianshu.io/upload_images/18410372-66e6131c411a785f.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/f14a41e8cfbe)
- [有免费的软件不用，为何要用付费的？](https://www.jianshu.com/p/5993b37a71ff)
    
    我们平常使用电脑经常会用到一些专业的软件，常见的像办公软件office、Adobe家族的软件、AutoCAD等等、...
    
    [![](https://upload.jianshu.io/users/upload_avatars/21581947/486cd976-8eb3-449b-b43b-52db9cd12076.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)甜orange](https://www.jianshu.com/u/953b08c9f936)阅读 4,316评论 8赞 26
    
    [![](https://upload-images.jianshu.io/upload_images/21581947-6e743c430bdbcf39.image?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/5993b37a71ff)

[![](https://upload.jianshu.io/users/upload_avatars/23633704/44b1ed66-2bbe-4790-ada0-40076482bf77?imageMogr2/auto-orient/strip|imageView2/1/w/90/h/90)](https://www.jianshu.com/u/bcef08d5551e)

[码不动](https://www.jianshu.com/u/bcef08d5551e)[<img width="16" height="16" src=":/ea42e96a675140739b11c8b0e270aa0b"/>](https://www.jianshu.com/mobile/creator)

总资产123 (约10.72元)

[CPU深夜狂飙，一帮大佬都傻眼了···](https://www.jianshu.com/p/87ce68417cdb)

阅读 3,008

[主板上这家伙，要当CPU和内存的中间商！](https://www.jianshu.com/p/a88177f0e9a3)

阅读 95

[Spring Boot 搭建 ELK，这才是正确看日志的方式！](https://www.jianshu.com/p/01a392a2fa27)

阅读 277

### 推荐阅读

[应急响应-综合汇总](https://www.jianshu.com/p/fa8736912791)

阅读 225

[今天，你的服务器被“挖矿”了吗？](https://www.jianshu.com/p/d2f93e4b7176)

阅读 193

[转：Linux服务器清除隐藏的挖矿病毒](https://www.jianshu.com/p/4be71d95de7d)

阅读 3,504

[可怕！那些你看不到的进程](https://www.jianshu.com/p/772365143a43)

阅读 350

[Ubuntu完全教程，让你成为Ubuntu高手！](https://www.jianshu.com/p/0ae245cfe1cf)

阅读 79,644

评论5

赞74

74赞75赞

赞赏

下载App