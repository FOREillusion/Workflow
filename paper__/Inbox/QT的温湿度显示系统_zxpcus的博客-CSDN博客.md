QT的温湿度显示系统_zxpcus的博客-CSDN博客

[<img width="80" height="44" src="../_resources/5086f16d76464cb8be8ff5c756b708c3.png"/>](https://www.csdn.net/)

- [博客](https://blog.csdn.net/)
- [学院](https://edu.csdn.net/)
- [下载](https://download.csdn.net/)
- [论坛](https://bbs.csdn.net/)
- [问答](https://ask.csdn.net/)
- [代码](https://codechina.csdn.net/?utm_source=csdn_toolbar)
- [招聘](https://job.csdn.net/)
- [电子书](https://book.csdn.net/)
- [VIP会员](https://mall.csdn.net/vip)

[创作中心](https://mp.csdn.net)

[收藏](https://i.csdn.net/#/uc/collection-list?type=1)

<a id="toolbar-remind"></a>[消息](https://live.csdn.net/room/weixin_47115905/bcobcpkN?utm_source=gonggao_1201)

[登录/注册](https://passport.csdn.net/account/login)

# QT的温湿度显示系统

<img width="36" height="32" src=":/a8a54abef6b44d09bd2c858be64ee58b"/>

[shi_kankan](https://blog.csdn.net/zxpcus) 2018-03-11 12:05:10 <img width="24" height="24" src=":/9aa1017359784c2d8e848b57b6329eca"/>6431 <a id="blog_detail_zk_collection"></a><img width="20" height="20" src="../_resources/64902c9f9bc54f56b2601bd2899d2ece.png"/>收藏 4 

分类专栏： [嵌入式Linux](https://blog.csdn.net/zxpcus/category_7454789.html) [Qt GUI](https://blog.csdn.net/zxpcus/category_7498269.html)

版权

大家好，这里是石侃侃的博客，欢迎前来讨论  

这篇文章介绍的是DHT11在2440上的基于Qt5.6的应用

  

基于单片机系统的温湿度监测系统构成框图如图2-1所示。

![](4f3e70f13d7e4044a6d2dd38a02a2c4b.png)  

图2-1  系统构成框图

该系统采用单片机s3c2440作为了本设计的核心元件。利用LCD作为了显示器件，利用触摸屏作为用户界面输入设备，第一行显示当前实时的温度，湿度，第二行依次显示历史时刻最高的温度值，最高的湿度值。第三行是一个文本框，记录了程序执行开始所有的温度，湿度数据。

其次，我们在设计中采用了QT作为界面GUI开发，QT以界面优美，代码优雅而广为人知，Qt是一个1991年由QtCompany开发的跨平台[C++](https://baike.baidu.com/item/C%2B%2B)[图形用户界面](https://baike.baidu.com/item/%E5%9B%BE%E5%BD%A2%E7%94%A8%E6%88%B7%E7%95%8C%E9%9D%A2)应用程序开发框架。它既可以开发[GUI](https://baike.baidu.com/item/GUI)程序，也可用于开发非GUI程序，比如控制台工具和服务器。Qt是面向对象的框架，使用特殊的代码生成扩展以及一些宏，Qt很容易扩展，并且允许真正地组件编程。

![](:/848a6c984f454b1eaa35064424fa3ca0)  

图2-2  界面功能图

### <a id="t0"></a><a id="t0"></a>实时显示功能

在设计方面贯彻了驱动和应用分层的原则，在加载了DHT11，已经注册DHT11设备的基础上，进行GUI  APP设计 。

在app设计中通过调用定时器实现100ms的界面刷新速度，在读取设备信息后，把当前时间的温度，湿度参数显示到GUI界面的第一行。

### <a id="t1"></a><a id="t1"></a>历史最高值设置

在实时监测当前温度与湿度的基础上，记录上一次的温度与湿度数据并进行比较，以便更新最高值数据到用户界面。

### <a id="t2"></a><a id="t2"></a>查看历史数据

   在文本框中存储系统的每一次接收到的温度和湿度数据，以供用户查看，可以通过FTP服务将数据传送到电脑。

## <a id="t3"></a><a id="t3"></a>温湿度监控系统的工作过程

整个温湿度监控系统的工作过程如下：单板上电首先执行BootLoader，初始化RAM、串口，检测处理器类型，调用 Linux内核映像。

启动kernel，创建异常向量表和初始化中断处理函数，初始化系统核心进程调度器和时钟中断处理机制；初始化串口控制台（serial-console）；ARM-Linux在初始化过程中一般都会初始化一个串口做为内核的控制台，这样内核在启动过程中就可以通过串口输出信息以便开发者或用户了解系统的启动进程。创建和初始化系统cache，为各种内存调用机制提供缓存，包括;动态内存分配，虚拟文件系统（VirtualFile System）及页缓存。初始化内存管理，检测内存大小及被内核占用的内存情况；初始化系统的进程间通信机制（IPC）。

启动文件系统，文件系统启动QT应用程序，运行test。

这是用户可以通过界面观察到当前和历史的温度湿度数据，并且通过触摸屏滑动滚动条查看历史信息。断电程序结束。

根据上述内容的描述，先进行主程序流程图的构思：

主程序设计流程图如图4-2所示：

                                                             ![](2b10fd1ff2dc4fb49b743a2acb216654.png)

图 4-2 主程序设计流程图

    首先我们进行系统软件的设计，上图是主程序流程图设计，进行系统初始化，然后读取传感器数据，对数据进行相应处理，得到历史最高值，在进行数据的保存操作，其次进行计时处理，最后是显示处理，每0.1S刷新一次界面。从读取数据到显示处理是一个循环的过程，进行显示处理判断后，来回反复地进行读取功能处理。  
资源链接：https://download.csdn.net/download/zxpcus/10280117  
  

-  <img width="22" height="22" src="../_resources/27809610fb8a472da994edc7f2aa0472.png"/> <a id="is-like-span"></a>点赞
- [<img width="22" height="22" src=":/e65024935293406788a304279e2d8d21"/>评论](#commentBox)
- <img width="22" height="22" src="../_resources/49f8b025e672479fbb0933f34b036cdf.png"/>分享
- <img width="22" height="22" src="../_resources/64902c9f9bc54f56b2601bd2899d2ece.png"/><a id="is-collection"></a>收藏 <a id="get-collection"></a>4 
- <a id="toolreward"></a><img width="22" height="22" src=":/9a30f822ac2c41dc84380e29e65d8330"/>打赏
- <img width="22" height="22" src=":/7bdbccb4786143298982888c752cedec"/>举报
- 关注
- 一键三连
    
    点赞Mark关注该博主, 随时了解TA的最新博文<img width="24" height="26" src=":/ff1c0ce5ac224e68b01e59916bbaeb97"/>

[*QT**温湿度*上位机](https://download.csdn.net/download/lidreamer/7306793)

05-06

[用*QT*写的监控*温湿度*的程序，很适合初学者，或者是老手参考](https://download.csdn.net/download/lidreamer/7306793)

[求助 *qt*编写温度*显示*界面](https://bbs.csdn.net/topics/380247545)

03-29

[哪位大侠手可以大概提点一下大概怎么编，或者给编个差不多的呗！辛苦了！](https://bbs.csdn.net/topics/380247545)

<a id="commentBox"></a>

<a id="commentsedit"></a>

<img width="30" height="30" src=":/af3333f915da4d5ab715c92da7059e37"/>

<img width="53" height="18" src="../_resources/560fbf132dda46a2beb1c2f90078b7a5.png"/>

[使用*Qt*串口实时*显示**温湿度*数据](https://blog.csdn.net/snowmcu20132514/article/details/90341800)

[snowmcu20132514的博客](https://blog.csdn.net/snowmcu20132514)

 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 5788 

[之前用*Qt*做了一个简单的串口助手，前几天做了一个可以实时*显示**温湿度*数据的串口应用，今天周末总结一下。 效果： 1、实现的效果如上图所示，可以实时*显示**温湿度*以及*系统*的当前时间； 2、串口的波特率可选，其它设置项固定，数据位8、停止位1、检验及流控无； 测试： 1、我使用了两种方式进行测试，第一个是通过短接串口线的接收和发送，在软件中定时发送固定格式的数据帧进行测试，我测试了大概4个小时，*系统*没有问...](https://blog.csdn.net/snowmcu20132514/article/details/90341800)

[基于*qt*的*温湿度*监控*系统*](https://download.csdn.net/download/jiangyang_123123qq/4915423)

12-21

[基于*qt*的*温湿度*监控*系统*，含设备驱动程序，可移植到开发板](https://download.csdn.net/download/jiangyang_123123qq/4915423)

[*温湿度*监控*系统*_*温湿度*监控*系统*代码,*qt*实时*显示**温湿度*-*QT*代码类...](https://download.csdn.net/download/twj5817/7303865)

12-4

[该*系统*实现了13个监控点,同时实现了使用qwt画实时曲线图,还支持监控数据写入数据库,同时可以在界面上查询并导出execl表格 *qt* qwt 实时曲线图 *温湿度*监控*系统*代码 ...](https://download.csdn.net/download/twj5817/7303865)

[jz2440的DHT11驱动+*QT*5.6的*温湿度*监测*系统*_*qt*实时*显示**温湿度*-嵌入...](https://download.csdn.net/download/zxpcus/10280117)

11-20

[*QT*的*温湿度**显示**系统* 6233大家好,这里是石侃侃的博客,欢迎前来讨论这篇文章介绍的是DHT11在2440上的基于*Qt*5.6的应用基于单片机*系统*的*温湿度*监测*系统*构成框图如图2-1...](https://download.csdn.net/download/zxpcus/10280117)

[温度采集器界面](https://download.csdn.net/download/huanyingwuzhede/10352822)

04-16

[温度采集器界面 集成了户表和热表 有热表的*显示*和设置！](https://download.csdn.net/download/huanyingwuzhede/10352822)

[*QT*和C++实现*显示*温度数据界面](https://blog.csdn.net/qq_39125908/article/details/107780916)

[qq_39125908的博客](https://blog.csdn.net/qq_39125908)

 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 393 

[1、确定需要的类，确定行为和属性。 2、面向过程，考虑数据流向。 首先要确定使用的温度计类大概需要什么函数。 如上图所示是大概需要的属性和行为。在电子温度计类继承的时候要注意是共有继承。否则无法正常访问父类的信息。 需要对值进行读取和*显示*。不可能一直不断的读，所以用到定时器。为整个程序的运行分配时间。 *QT*imer *t1 = new *QT*imer(); // signals and slots .1 event creat 1 sig. slots deal..](https://blog.csdn.net/qq_39125908/article/details/107780916)

[*Qt*实现天气预报与PM2.5监测*系统*(1)项目介绍_91创客学堂](https://blog.csdn.net/tony_shen/article/details/65937997)

12-5

[*系统*软件全部在开发板运行。 *Qt*程序实现界面数据*显示*,从串口读取PM2.5数据,从设备文件读取*温湿度*数据。 Python程序天气API接口从网络获取天气预报数据。](https://blog.csdn.net/tony_shen/article/details/65937997)

[*Qt*实现天气预报与PM2.5监测*系统*(7)今日天气_91创客学堂](https://blog.csdn.net/tony_shen/article/details/69384377)

11-25

[*Qt*实现天气预报与PM2.5监测*系统*(7)今日天气 程序界面 主要*显示*两部分内容:1.通过云端的API接口获取的天气数据,2.通过本地的 PM2.5 与*温湿度*模块获取的数据。](https://blog.csdn.net/tony_shen/article/details/69384377)

[*Qt*实例：湿度计(绘画)](https://blog.csdn.net/ljwoainia/article/details/102779540)

[长安](https://blog.csdn.net/ljwoainia)

 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 514 

[一 效果图 二 代码 2.1 头文件 mainwindow.h 
~~~ #ifndef MAINWINDOW\_H #define MAINWINDOW\_H #include &lt;QMainWindow&gt; #include "mainwindow.h" #include "ui_mainwindow.h" #include &lt;QDebug&gt; #include <QDa...]
~~~
(https://blog.csdn.net/ljwoainia/article/details/102779540)


[*qt**温湿度*上位机源码](https://download.csdn.net/download/qq_41293340/10998243)

03-05

[*qt*5*温湿度*上位机界面，包含通讯部分。](https://download.csdn.net/download/qq_41293340/10998243)

[基于*QT*4的智能温度采集控制*系统*_91创客学堂](https://blog.csdn.net/tony_shen/article/details/8770987)

12-6

[2.采集*显示*,5秒自动更新从DS18采集的温度数据。 3.温度报警设置,如果采集到的温度超过设置范围*系统*报警。 注意:要保存后,数据才生效。](https://blog.csdn.net/tony_shen/article/details/8770987)

[基于Zigbee与*Qt*的室内环境检测*系统*——详细介绍*QT*部分](https://blog.csdn.net/qq_43961980/article/details/100414550)

12-4

[*Qt*界面接受在*显示**温湿度*、甲烷含量数据的同时会按固定时间间隔来存储相关数据,便于用户查看并了解室内环境的过往情况(数据查询代码如图4)。在数据库中的储存方式上我们...](https://blog.csdn.net/qq_43961980/article/details/100414550)

[*Qt* creator 界面设计 *系统*运行时间差计算并实时*显示*](https://bbs.csdn.net/topics/392378553)

05-14

[1.如图，目的是：计算*系统*已经运行的时间并实时*显示*在最下面（objectname为interval）的label上 （1）其中生产时间后面的label（objectname为timeLabel）是实](https://bbs.csdn.net/topics/392378553)

[C程序输出数据，在*QT*界面上的lineedit中实时*显示*，用什么方法？](https://bbs.csdn.net/topics/380092788)

12-08

[C程序输出数字0~9，或者一串字符串，怎么在*QT*界面上实时*显示*在*QT*界面上的lineedit中，求方法，求代码，万分感谢！！！](https://bbs.csdn.net/topics/380092788)

[基于物联网实验验环境的*温湿度*实时*显示**系统*-C代码类资源](https://download.csdn.net/download/qq_29777421/9892878)

12-3

[基于物联网实验验环境的*温湿度*实时*显示**系统* *温湿度*数据同通过串口从传感器模块中获得 *QT* 物联网 立即下载 低至0.43元/次 身份认证VIP会员低至7折 ...](https://download.csdn.net/download/qq_29777421/9892878)

©️2020 CSDN 皮肤主题: 编程工作室 设计师:CSDN官方博客 [返回首页](https://blog.csdn.net/)

- [关于我们](https://www.csdn.net/company/index.html#about)
- [招贤纳士](https://www.csdn.net/company/index.html#recruit)
- [广告服务](https://www.csdn.net/company/index.html#advertisement)
- [开发助手](https://plugin.csdn.net/)
- <img width="16" height="16" src="../_resources/1240fd5c7b4f45edb57c0b4e8aee58fd.png"/>400-660-0108
- <img width="16" height="16" src="../_resources/2eaa1aee5b584b3895c8e4c06945a696.png"/>[kefu@csdn.net](mailto:webmaster@csdn.net)
- <img width="16" height="16" src="../_resources/3a7007708f6d4c9a8d93319330093ff0.png"/>[在线客服](https://csdn.s2.udesk.cn/im_client/?web_plugin_id=29181)
- 工作时间 8:30-22:00

- [公安备案号11010502030143](http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=11010502030143)
- [京ICP备19004658号](http://beian.miit.gov.cn/publish/query/indexFirst.action)
- [京网文〔2020〕1039-165号](https://csdnimg.cn/release/live_fe/culture_license.png)
- [经营性网站备案信息](https://csdnimg.cn/cdn/content-toolbar/csdn-ICP.png)
- [北京互联网违法和不良信息举报中心](http://www.bjjubao.org/)
- [网络110报警服务](http://www.cyberpolice.cn/)
- [中国互联网举报中心](http://www.12377.cn/)
- [家长监护](https://download.csdn.net/index.php/tutelage/)
- [Chrome商店下载](https://chrome.google.com/webstore/detail/csdn%E5%BC%80%E5%8F%91%E8%80%85%E5%8A%A9%E6%89%8B/kfkdboecolemdjodhmhmcibjocfopejo?hl=zh-CN)
- ©1999-2020北京创新乐知网络技术有限公司
- [版权与免责声明](https://www.csdn.net/company/index.html#statement)
- [版权申诉](https://blog.csdn.net/blogdevteam/article/details/90369522)

[![](3b9a37b9e97d4a74bc984f7e86c80a2e.jpg)](https://blog.csdn.net/zxpcus)

<a id="uid"></a>[shi_kankan](https://blog.csdn.net/zxpcus "shi_kankan")

码龄5年 [<img width="12" height="14" src=":/bc03f36c7b66482bbe5d4bf34a78be6d"/> 暂无认证](https://blog.csdn.net/zxpcus?utm_source=14998968 "暂无认证") 

[15<br>原创](https://blog.csdn.net/zxpcus)

[31万+<br>周排名](https://blog.csdn.net/rank/writing_rank)

[20万+<br>总排名](https://blog.csdn.net/rank/writing_rank_total)

2万+

访问

[<img width="22" height="22" src=":/de5b94d9d44d4204b2392ee5575158a3"/>](https://blog.csdn.net/home/help.html#level)

等级

445

积分

<a id="fan"></a>9

粉丝

7

获赞

2

评论

23

收藏

<img width="36" height="36" src=":/966a0a3546464a4bbf335341b51c1073"/>

[私信](https://im.csdn.net/chat/zxpcus)

<a id="btnAttent"></a>关注

<img width="32" height="32" src="../_resources/1c24213399b7496ab2623e5dee2b963e.png"/>

### 热门文章

- [libjpeg-turbo 的安装和简单使用 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 8718](https://blog.csdn.net/zxpcus/article/details/80104772)
- [QT的温湿度显示系统 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 6383](https://blog.csdn.net/zxpcus/article/details/79514948)
- [halcon 灰度和高通滤波 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 3636](https://blog.csdn.net/zxpcus/article/details/80143722)
- [STM32黑线 黑点图像识别 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 2614](https://blog.csdn.net/zxpcus/article/details/78334420)
- [搭建大恒相机开发环境 <img width="14" height="14" src=":/a22230aa440d4b1985c6ba6758d7e8b4"/> 2150](https://blog.csdn.net/zxpcus/article/details/79728135)

### 分类专栏

-  [<img width="28" height="28" src="../_resources/44f47d84338149688baadfde79c58515.png"/> ping，Linux 1篇](https://blog.csdn.net/zxpcus/category_7212331.html)
- [<img width="28" height="28" src=":/d09176d8b3704e20a45832c520fa96dd"/>stm32； 黑点黑线识别 1篇](https://blog.csdn.net/zxpcus/category_7246636.html)
- [<img width="28" height="28" src=":/8a91294637214a1687dced6bd2f97c7f"/>嵌入式Linux 8篇](https://blog.csdn.net/zxpcus/category_7454789.html)
- [<img width="28" height="28" src=":/8a91294637214a1687dced6bd2f97c7f"/>Qt GUI 1篇](https://blog.csdn.net/zxpcus/category_7498269.html)
- [<img width="28" height="28" src=":/9e9863f899184c96b3f77eda9063c23a"/>MFC 4篇](https://blog.csdn.net/zxpcus/category_7533424.html)
- [<img width="28" height="28" src=":/8a91294637214a1687dced6bd2f97c7f"/>数字图像处理 2篇](https://blog.csdn.net/zxpcus/category_7630021.html)

<img width="12" height="7" src=":/a171a1373b4e40ca968153c57f24a138"/>

### 最新评论

- [mfc运行原理分析](https://blog.csdn.net/zxpcus/article/details/79672496#comments_12702006)
    
    [翻斗大街之王:](https://blog.csdn.net/qq_40313487) 写的很好
    
- [LINUX 3.4.2 SD卡挂载失败的解决办法](https://blog.csdn.net/zxpcus/article/details/82428965#comments_11878959)
    
    [timmy_chan:](https://blog.csdn.net/timmy_chan) 楼主你的意思是不是linux3.4.2内置的s3cmci.c生成的驱动并不能正常挂载SD卡是吗？
    

### 最新文章

- [LINUX 3.4.2 SD卡挂载失败的解决办法](https://blog.csdn.net/zxpcus/article/details/82428965)
- [opencv for arm](https://blog.csdn.net/zxpcus/article/details/80186623)
- [halcon 灰度和高通滤波](https://blog.csdn.net/zxpcus/article/details/80143722)

[2018年13篇](https://blog.csdn.net/zxpcus/article/month/2018/09)

[2017年2篇](https://blog.csdn.net/zxpcus/article/month/2017/10)

### 目录

1.  [实时显示功能](#t0)
2.  [历史最高值设置](#t1)
3.  [查看历史数据](#t2)
4.  [温湿度监控系统的工作过程](#t3)

 <img width="24" height="24" src=":/d4b1f071bbfb44f3a43d7100d3744c38"/> <img width="24" height="24" src=":/a2bee5ca8c0b4ac9b596309933ba4444"/>举报 <img width="24" height="24" src=":/ebe2b6b0124b4f3c94503dd4ca9cbd09"/>