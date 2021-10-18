(4条消息) Nvidia JetsonTx2 使用中遇到的坑与填坑方法_shao455的博客-CSDN博客



[<img width="32" height="32" src="../_resources/4e47142c0ebd4b9694ecd5c1115dd648.gif"/>](https://i.csdn.net)

# Nvidia JetsonTx2 使用中遇到的坑与填坑方法

<img width="36" height="32" src=":/8147c81d677346f4950a2dfc9d91e1d7"/>

[xnshall](https://me.csdn.net/shao455) 2019-04-09 17:43:05 <img width="24" height="24" src="../_resources/312f18bc769843dbb06b57363b39be77.png"/>3073 <a id="blog_detail_zk_collection"></a><img width="20" height="20" src=":/f63c176bd6ff44b7a50cf0368bbbf4ec"/>收藏 5 

分类专栏： [Nvidia Jetson Tx2](https://blog.csdn.net/shao455/category_8841794.html)

版权

## <a id="t0"></a><a id="t0"></a><a id="1_Tx2_cudaJetPackTx2JetPack_33_0"></a>1\. Tx2 买回来后最好刷机，自带系统没有cuda等驱动，重新刷JetPack包。Tx2刷JetPack 3.3方法：

[https://blog.csdn.net/long19960208/article/details/81538997?utm_source=blogxgwz1](https://blog.csdn.net/long19960208/article/details/81538997?utm_source=blogxgwz1)

注意：主机PC安装过程中会弹出下面窗口，不要关闭，连接tx2刷机模式，连接上后在该窗口中enter  
![在这里插入图片描述](:/f1750ffefa274706bb334f1a0894e46c)

## <a id="t1"></a><a id="t1"></a><a id="2_Tx2_ROS_Kinetic_6"></a>2\. Tx2 安装ROS Kinetic

#### <a id="21__7"></a>2.1 更换源

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.back
sudo gedit /etc/apt/sources.list
12
```

deb [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial-updates main restricted universe multiverse  
deb-src [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial-updates main restricted universe multiverse  
deb [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial-security main restricted universe multiverse  
deb-src [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial-security main restricted universe multiverse  
deb [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial-backports main restricted universe multiverse  
deb-src [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial-backports main restricted universe multiverse  
deb [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial main universe restricted  
deb-src [http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/](http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/) xenial main universe restricted

```
sudo apt-get update
git clone https://github.com/jetsonhacks/installROSTX2.git

./installROS.sh -p ros-kinetic-desktop-full
1234
```

## <a id="t2"></a><a id="t2"></a><a id="3_OpenCV_Tx2__26"></a>3\. 使用OpenCV启动 Tx2 板载摄像头

#### <a id="31__27"></a>3.1 命令方式启动

**启动方法1：**

```
gst-launch-1.0 nvcamerasrc fpsRange="30.0 30.0" ! 'video/x-raw(memory:NVMM), width=(int)1920, height=(int)1080, format=(string)I420, framerate=(fraction)30/1' ! nvtee ! nvvidconv flip-method=2 ! 'video/x-raw(memory:NVMM), format=(string)I420' ! nvoverlaysink -e
1
```

参数：flip-method指定图像旋转模式，可选0,1,2等，没有全部试验，**0显示正常**，1旋转90度，2上下镜像，该相机获取的图像偏暗。

**启动方法2：**  
–prev_res 预览视屏的分辨率：

```
nvgstcapture-1.0 --prev-res=2
1
```

**启动方法3**，指定分辨率

```
nvgstcapture-1.0 --cus-prev-res=640x480
1
```

Tx2支持的分辨率有：  
(2) : 640x480  
(3) : 1280x720  
(4) : 1920x1080  
(5) : 2104x1560  
(6) : 2592x1944  
(7) : 2616x1472  
(8) : 3840x2160  
(9) : 3896x2192  
(10): 4208x3120  
(11): 5632x3168  
(12): 5632x4224

#### <a id="32_OpenCV__54"></a>3.2 OpenCV 启动

需要使用包含gsteramer的opencv  
首先，卸载系统安装的OpenCV

```
sudo apt-get purge libopencv*
sudo apt-get update
12
```

如果源码安装了opencv，卸载源码安装的opencv

```
cd opencv/build
sudo make uninstall
cd ..
sudo rm -r build
sudo rm -r /usr/local/include/opencv2 /usr/local/include/opencv /usr/include/opencv /usr/include/opencv2 /usr/local/share/opencv /usr/local/share/OpenCV /usr/share/opencv /usr/share/OpenCV /usr/local/bin/opencv* /usr/local/lib/libopencv*
12345
```

安装依赖包

```
sudo apt-get update 
sudo apt-get install build-essential cmake git pkg-config 
sudo apt-get install libjpeg8-dev libtiff4-dev libjasper-dev libpng12-dev 
sudo apt-get install libgtk2.0-dev 
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev 
sudo apt-get install libatlas-base-dev gfortran 

sudo apt-get install -y libgstreamer1.0-0 \
            gstreamer1.0-plugins-base \
            gstreamer1.0-plugins-good \
            gstreamer1.0-plugins-bad \
            gstreamer1.0-plugins-ugly \
            gstreamer1.0-libav \
            gstreamer1.0-doc \
            gstreamer1.0-tools \
            libgstreamer1.0-dev \
            libgstreamer-plugins-base1.0-dev
1234567891011121314151617
```

下载opencv 3.3.1源码包

开启Tx2高性能模式加速编译

```
sudo nvpmodel -m 0
cd 
sudo ./jetson_clocks.sh
123
```

编译opencv

```
cd $HOME/3rdLibrary/opencv-3.3.1
mkdir build
cd build 
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    -DBUILD_PNG=OFF \
    -DBUILD_TIFF=OFF \
    -DBUILD_TBB=OFF \
    -DBUILD_JPEG=OFF \
    -DBUILD_JASPER=OFF \
    -DBUILD_ZLIB=OFF \
    -DBUILD_EXAMPLES=ON \
    -DBUILD_opencv_java=OFF \
    -DBUILD_opencv_python2=ON \
    -DBUILD_opencv_python3=ON \
    -DENABLE_PRECOMPILED_HEADERS=OFF \
    -DWITH_OPENCL=OFF \
    -DWITH_OPENMP=OFF \
    -DWITH_FFMPEG=ON \
    -DWITH_GSTREAMER=ON \
    -DWITH_GSTREAMER_0_10=OFF \
    -DWITH_CUDA=ON \
    -DWITH_GTK=ON \
    -DWITH_VTK=OFF \
    -DWITH_TBB=ON \
    -DWITH_1394=OFF \
    -DWITH_OPENEXR=OFF \
    -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-9.0 \
    -DCUDA_ARCH_BIN=6.2 \
    -DCUDA_ARCH_PTX="" \
    -DINSTALL_C_EXAMPLES=OFF \
    -DINSTALL_TESTS=OFF \
    ../

make -j6
sudo make install
sudo ldconfig
1234567891011121314151617181920212223242526272829303132333435363738
```

配置环境变量  
1.`sudo gedit /etc/ld.so.conf`  
末行加入：`include /usr/local/lib`

2.执行指令，使路径生效：

```
   sudo ldconfig
1
```

3.修改bash.bashrc文件

```
sudo gedit /etc/bash.bashrc
1
```

末行加入：

```
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
export PKG_CONFIG_PATH 
12
```

4.最后source一下，让更改立即生效，终端输入：

```
source /etc/bash.bashrc  
1
```

5.查看 OpenCV 安装版本

```
pkg-config --modversion opencv
1
```

## <a id="t3"></a><a id="t3"></a><a id="4_Tx2__Xbox_one_159"></a>4\. Tx2 蓝牙连接 Xbox one手柄

Tx2的内核不支持蓝牙手柄连接，连接上后时断时连，无法连接成功，USB同样无法连接成功  
`ls /dev/input/` 不显示手柄 js*  
需要重新编译内核，将 joystick 添加进内核.  
可参考：[https://www.ncnynl.com/archives/201706/1752.html](https://www.ncnynl.com/archives/201706/1752.html)  
该方法本人试验失败，重新编译后仍然不能连接成功

-  <img width="22" height="22" src="../_resources/4147a43ffd364633b0d8e8f6d006363a.png"/> <a id="is-like-span"></a>点赞 <a id="spanCount"></a>1 
- [<img width="22" height="22" src=":/c85494d922c145edaa79e9f26c1b0470"/>评论](#commentBox)
- <img width="22" height="22" src="../_resources/20029f519d8d428cb5093e1f70bd24ad.png"/>分享
- <img width="22" height="22" src=":/f63c176bd6ff44b7a50cf0368bbbf4ec"/><a id="is-collection"></a>收藏 <a id="get-collection"></a>5 
-  <img width="22" height="22" src=":/b1ee2aa0476b47ac964904fffcc70fc9"/> 手机看 
- <a id="toolreward"></a><img width="22" height="22" src=":/eb7b6654045b4ebcb5ddf51a07b9ebe7"/>打赏
- <img width="17" height="3" src=":/8b96697fc2d141aaba0fbc2cf6ce0868"/>
- 关注

[*JetsonTX*1_TX*2*说明文档](https://download.csdn.net/download/hnsywangxin/10221798)

01-24

[该文档为目前为止最详细哦TX1,TX*2*的说明文档，对这两块板子感兴趣的可以下载好好研究](https://download.csdn.net/download/hnsywangxin/10221798)

[*NVIDIA* Jetson TX*2* *使用*总结](https://blog.csdn.net/Zhaoxi_Li/article/details/86589799)

[小玺大大](https://blog.csdn.net/Zhaoxi_Li)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 3108 

[这里记录下TX*2*的一些操作总结（不定期补充），涉及到一些注意事项，及其一些工具包的安装 文章目录1 刷机部分1.1刷机到本机系统1.*2* 刷机到SDCard*2* 安装TensorFlow*2*.1 分区*2*.*2* 安装依赖 1 刷机部分 1.1刷机到本机系统 关于TX*2*的刷机部分，其他博客都已经介绍了许多了。这里主要注意几点，强烈建议TX*2*用有线网连接，免得Wifi突然断掉。 1.*2* 刷机到SDCard 目前...](https://blog.csdn.net/Zhaoxi_Li/article/details/86589799)

<a id="commentBox"></a>

<a id="commentsedit"></a>

[<img width="30" height="30" src=":/a0dac7151fd947d69bd8d1983b4f74b4"/>](https://me.csdn.net/qq_43528695)

<img width="46" height="18" src=":/9bbb5f0edf3046ba9a51eeeacb3c596b"/>

[*NVIDIA* Jetson TX*2* 挂载SSD硬盘](https://blog.csdn.net/jiangchao3392/article/details/73549680)

[jiangchao3392的专栏](https://blog.csdn.net/jiangchao3392)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 7298 

[安装环境：          硬件平台： *NVIDIA* Jetson TX*2*，   三星 SSD 850EVO         系统平台： ubuntu 16.04  LTS 1，查看硬盘所有分区并记录下待挂在分区（主要以硬盘，分区大小和格式确定）       硬盘格式为MBR， 非GPT。sudo fdisk -lu ２、对硬盘进行分区。在终端窗口*中*输](https://blog.csdn.net/jiangchao3392/article/details/73549680)

[说说今天刷*Nvidia* TX*2*踩的*坑*](https://blog.csdn.net/Doomhammer/article/details/77751127)

[Doomhammer的博客](https://blog.csdn.net/Doomhammer)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 6280 

[1、官网上说主机上需要至少10GB的硬盘空间。错，*2*0GB也不够用，至少30GB *2*、 TX*2*可以用wifi与主机进行ssh通信吗。不行，必须用网线，主机可以用wifi 3、 TX*2*可以用VGA接口接显示器吗。不行，必须HDMI 4、 可以用Ubuntu 16刷机吗。不行，必须ubuntu 14.04](https://blog.csdn.net/Doomhammer/article/details/77751127)

[Jetson TX*2* 刷机*中**遇到*的奇葩大*坑*_henzi000的博客-CSDN博客](https://blog.csdn.net/henzi000/article/details/88544764)

9-11

[想到终极方案,Flashing TX*2*! 本来以为刷个机1天的事情,网上一大堆教程,结果有失算了,整个过程的*坑*太多,差点放弃了,为了后来这不走我的老路,把我*遇到*的*坑*列一下...](https://blog.csdn.net/henzi000/article/details/88544764)

[Jetson tx*2*刷机过程*中*的*坑*_night李的博客-CSDN博客](https://blog.csdn.net/qq_25819827/article/details/77658015)

9-10

[Jetson tx*2*刷机过程*中*的*坑* night李*2*017-08-*2*8 *2*3:08:09153*2*6收藏4 分类专栏:CUDA 暑假各种事忙得差不多后,终于有时间拿出早就申请到的tx*2*,开始刷机教程,这两天...](https://blog.csdn.net/qq_25819827/article/details/77658015)

[TX*2*入门教程基础篇-介绍](https://blog.csdn.net/zhenguo26/article/details/79630160)

[zhenguo26的博客](https://blog.csdn.net/zhenguo26)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 6305 

[转载： https://www.ncnynl.com/archives/*2*01706/1736.htmlTX*2*入门教程基础篇-介绍TX*2*入门教程基础篇-介绍说明：介绍TX*2*嵌入式开发板Tegra X*2*:Jetson TX*2* 延续了该系列体积小巧、高度集成的特性，大小仅相当于一张信用卡。与前一代Jetson TX1相比，TX*2*能提供两倍的功效。GPU、CPU都进行了升级，其*中*GPU变成了Pascal...](https://blog.csdn.net/zhenguo26/article/details/79630160)

[计算机组成原理实验教程](https://download.csdn.net/download/theresanan/3874291)

12-03

[西北工业大学计算机组成原理实验课唐都仪器实验帮助，同实验指导书。分为运算器，存储器，控制器，模型计算机，输入输出系统5个章节](https://download.csdn.net/download/theresanan/3874291)

[*nvidia* jetson tx*2* 配置过程*遇到*的问题总结_yo跟着新宇走-CSDN博客](https://blog.csdn.net/bluehatihati/article/details/105601447)

9-11

[拿到一块*nvidia* jetson tx*2* 开发板,进行研究。 由于其采用arm架构,异于大部分计算终端的x86架构,所以在配置的过程*中**遇到*了很多问题。现总结如下。](https://blog.csdn.net/bluehatihati/article/details/105601447)

[Jetson TX*2* 刷机时*遇到*的*坑*_zshluckydogs的博客-CSDN博客](https://blog.csdn.net/zshluckydogs/article/details/79855631)

9-10

[password: *nvidia* or ubuntu 默认是这两个,但一般是默认*nvidia*。 这还不够,依然会提示无法连接。这时需要修改主机的ip。 图*中*的IP是你在TX*2*上查到的静态IP。](https://blog.csdn.net/zshluckydogs/article/details/79855631)

[*Nvidia* Jetson TX*2* 刷机*遇到*的问题及解决*方法*](https://blog.csdn.net/qq_42075882/article/details/107376186)

[qq_42075882的博客](https://blog.csdn.net/qq_42075882)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 231 

[*Nvidia* Jetson TX*2* 刷机*遇到*的问题及解决*方法* 从开始了解TX*2*到刷机完成用了大概两周的时间，粗略的记录了一下过程以及自己*遇到*的问题，希望能帮到大家。 步骤参考 Host主机：要求是Linux系统，虚拟机也可以。Target：*Nvidia* Jetson TX*2* 在主机上的操作（以虚拟机为例）： 确定SDK安装版本https://developer.*nvidia*.com/embedded/jetpack-archive 找到匹配的Ubuntu版本 下载VMware和Ubuntu相应版本 Ubu](https://blog.csdn.net/qq_42075882/article/details/107376186)

[在jetson tx*2*上*使用*串口通信](https://blog.csdn.net/zomb1e0117/article/details/85157014)

[zomb1e0117的博客](https://blog.csdn.net/zomb1e0117)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 7483 

[0.串口的查看 在Jetson TX*2*上共有5路UART接口，通过*使用*命令： ls -l /dev/ttyTHS* 可以查看在设备上已经启用的串口，结果如下： Jetson TX*2*默认启用两路串口，其*中*的ttyTHS1和ttyTHS3分别是控制台串口和蓝牙模块，而ttyTH*2*就是我们要*使用*的串口。 该串口位于位于J17模块处。如图所示，J17为Serial Port Heade...](https://blog.csdn.net/zomb1e0117/article/details/85157014)

[Jetson tx*2*刷机过程*中*的*坑* \- night李的博客 - CSDN博客](https://blog.csdn.net/qq_25819827/article/details/77658015?locationNum=9&fps=1)

11-14

[0 写在前面由于项目需要,*使用**NVIDIA* Jetson TX*2*作为硬件开发平台,在此记录*使用**方法*和*遇到*的问题。 *NVIDIA* Jetson TX*2*是英伟达的嵌入式开发套件,可以进行视觉计算......](https://blog.csdn.net/qq_25819827/article/details/77658015?locationNum=9&fps=1)

[Jetson tx*2*刷机过程*中*的*坑*\_weixin\_343183*2*6的博客-CSDN博客](https://blog.csdn.net/weixin_34318326/article/details/90327151)

9-8

[暑假各种事忙得差不多后,终于有时间拿出早就申请到的tx*2*,开始刷机教程,这两天几乎踩边了所有的*坑*。 第一个*坑*,虚拟机 一般在安装VMware虚拟机时,建议的安装空间...](https://blog.csdn.net/weixin_34318326/article/details/90327151)

[*NVIDIA* JetSON TX*2* 爬*坑*日记（一）](https://blog.csdn.net/weixin_36882833/article/details/84645490)

[weixin_36882833的博客](https://blog.csdn.net/weixin_36882833)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 431 

[*NVIDIA* JetSON TX*2* 爬*坑*日记（一） 拿到TX*2*开机后，由于没有提供电源线我只好把同事显示器的电源线拔了下来，反正他也不用。接上电源，按下边上的电源灯点亮屏幕。 1、根据命令行界面提示，一顿安装后，只需要几步就可以安装桌面驱动进入桌面。这里很简单只需要最基本的命令行操作。 *2*、进入后我们拿到的是最简单的ubunu16.04的操作系统，干干净净 3、接下来开始根据官网刷机了Jets...](https://blog.csdn.net/weixin_36882833/article/details/84645490)

[*NVIDIA* Jetson TK1学习与开发（六）：如何安装CUDA](https://blog.csdn.net/pengz0807/article/details/50850209)

[Zeng Peng的博客](https://blog.csdn.net/pengz0807)

 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 1161 

[版权声明：本文为博主原创文章，转载请标注转载网址：http://blog.csdn.net/frd*2*009041510 本文介绍如何安装CUDA，以CUDA6.0为例介绍。 1、Installing the CUDA Toolkit onto your device for native CUDA development Download the .deb file fo](https://blog.csdn.net/pengz0807/article/details/50850209)

[TX*2* 入*坑*总结(持续更新)\_weixin\_40554881的博客-CSDN博客](https://blog.csdn.net/weixin_40554881/article/details/80835629)

9-5

[TX*2* 入*坑*总结(持续更新) 1 新机无法*使用*hdmi转VGA连接显示器,就算是换成1080p的显示器也不行(之前的树莓派可是啥都不挑各种连)。更新内核后就可以连接1080p的...](https://blog.csdn.net/weixin_40554881/article/details/80835629)

©️2020 CSDN 皮肤主题: 技术黑板 设计师:CSDN官方博客 [返回首页](https://blog.csdn.net/)

[关于我们](https://www.csdn.net/company/index.html#about) [招聘](https://www.csdn.net/company/index.html#recruit) [广告服务](https://www.csdn.net/company/index.html#advertisement) [网站地图](https://www.csdn.net/gather/A) *[kefu@csdn.net](mailto:webmaster@csdn.net) [客服论坛](http://bbs.csdn.net/forums/Service) 400-660-0108 [QQ客服（8:30-22:00）](https://url.cn/5epoHIm?_type=wpa&qidian=true)*

[公安备案号 11010502030143](http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=11010502030143) [京ICP备19004658号](http://beian.miit.gov.cn/publish/query/indexFirst.action) [京网文〔2020〕1039-165号](https://csdnimg.cn/release/live_fe/culture_license.png) [版权与免责声明](https://www.csdn.net/company/index.html#statement) [版权申诉](https://blog.csdn.net/blogdevteam/article/details/90369522) [网络110报警服务](http://www.cyberpolice.cn/)

[中国互联网举报中心](http://www.12377.cn/) [家长监护](https://download.csdn.net/index.php/tutelage/) [版权申诉](https://blog.csdn.net/blogdevteam/article/details/90369522) [北京互联网违法和不良信息举报中心](http://www.bjjubao.org/) ©1999-2020 北京创新乐知网络技术有限公司

[![](1c85e34ba96a45679a28836608629848.jpg)](https://blog.csdn.net/shao455)

<a id="uid"></a>[xnshall](https://blog.csdn.net/shao455 "xnshall")

码龄10年 [<img width="12" height="14" src="../_resources/2b6d56695e6b4ca0b0def379a6402bd1.png"/> 暂无认证](https://me.csdn.net/shao455?utm_source=14998968 "暂无认证") 

[3<br>原创](https://blog.csdn.net/shao455)

[64万+<br>周排名](https://blog.csdn.net/rank/writing_rank)

[63万+<br>总排名](https://blog.csdn.net/rank/writing_rank_total)

3878

访问

[<img width="22" height="22" src=":/8e7b4cd976d34634ac28e6ef6ab4d4bc"/>](https://blog.csdn.net/home/help.html#level)

等级

64

积分

<a id="fan"></a>0

粉丝

3

获赞

0

评论

6

收藏

[TA的主页](https://me.csdn.net/shao455)

[私信](https://im.csdn.net/chat/shao455)

<a id="btnAttent"></a>关注

<img width="32" height="32" src="../_resources/2002dd7fa41a48b1a672f5bdfa35e557.png"/>

### 热门文章

- [Nvidia JetsonTx2 使用中遇到的坑与填坑方法 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 3032](https://blog.csdn.net/shao455/article/details/89156380)
- [Kinect2 连接 Turtlebot2 底座连接线 DIY <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 635](https://blog.csdn.net/shao455/article/details/84325083)
- [win10+Ubuntu双系统安装 <img width="14" height="14" src=":/b14709b36c6641629bb5142d0d856aea"/> 59](https://blog.csdn.net/shao455/article/details/88887468)

### 分类专栏

-  [<img width="28" height="28" src=":/79943c78d7f841a0849d925377ad4fbd"/> ROS 1篇](https://blog.csdn.net/shao455/category_8403445.html)
- [<img width="28" height="28" src="../_resources/698f4547ad2c43ac8a823a7887efbc92.png"/>Ubuntu 1篇](https://blog.csdn.net/shao455/category_8807295.html)
- [<img width="28" height="28" src="../_resources/16fe25590dcc409785f49c37e2122a3b.png"/>Nvidia Jetson Tx2 1篇](https://blog.csdn.net/shao455/category_8841794.html)

### 最新文章

- [win10+Ubuntu双系统安装](https://blog.csdn.net/shao455/article/details/88887468)
- [Kinect2 连接 Turtlebot2 底座连接线 DIY](https://blog.csdn.net/shao455/article/details/84325083)

[2019年2篇](https://blog.csdn.net/shao455/article/month/2019/04)

[2018年1篇](https://blog.csdn.net/shao455/article/month/2018/11)

### 目录

1.  [1\. Tx2 买回来后最好刷机，自带系统没有cuda等驱动，重新刷JetPack包。Tx2刷JetPack 3.3方法：](#t0)
2.  [2\. Tx2 安装ROS Kinetic](#t1)
3.  [3\. 使用OpenCV启动 Tx2 板载摄像头](#t2)
4.  [4\. Tx2 蓝牙连接 Xbox one手柄](#t3)

 <img width="24" height="24" src="../_resources/5099bb9a37194f8fa39652488d88cceb.png"/> <img width="24" height="24" src="../_resources/51661c2856af40d4b06c920ca137436d.png"/>举报 <img width="24" height="24" src=":/d6e501c6531f4acfbb465374947d390d"/>