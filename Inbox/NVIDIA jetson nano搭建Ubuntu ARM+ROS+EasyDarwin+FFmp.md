NVIDIA jetson nano搭建Ubuntu ARM+ROS+EasyDarwin+FFmpeg+x264+RTSP推流服务器 | | 食铁兽

[Skip to content](#content)

[食铁兽](https://feater.top/)

只是入门而已

- [入门系列](https://feater.top/category/series/)
    - [FFmpeg4入门系列](https://feater.top/series/ffmpeg/78/)
    - [OpenCV4入门系列(整理中)](https://feater.top/series/opencv/74/)
    - [OpenGL入门系列](https://feater.top/series/opengl/1225/)
- [电子书](https://feater.top/category/ebooks/)
- [说明](https://feater.top/category/about/)
    - [关于本站](https://feater.top/about/8/)
    - [工作推荐](https://feater.top/about/705/)
    - [网站日志](https://feater.top/about/563/)

![](:/a94baa548e6c4d62b7dc0a37906e6b5a)

[2020年4月30日](https://feater.top/series/ffmpeg/280/ "下午12:34") [FFmpeg](https://feater.top/category/series/ffmpeg/)

# NVIDIA jetson nano搭建Ubuntu ARM+ROS+EasyDarwin+FFmpeg+x264+RTSP推流服务器

![](:/e3012380c07e4b58b4b5c856fd27f5e8)Posted by  [JackeyLea](https://feater.top/author/jackeylea/ "View all posts by JackeyLea")

点击数：119

Contents

- [1 公司任务](#i)
- [2 前提](#i-2)
- [3 准备](#i-3)
- [4 编译安装](#i-4)
    - [4.1 x264](#x264)
    - [4.2 ffmpeg](#ffmpeg)
    - [4.3 go](#go)
- [5 node](#node)
- [6 easydarwin](#easydarwin)
- [7 测试](#i-5)

# <a id="i"></a>公司任务

基于ROS NVIDIA jetson nano搭建easydarwin+ffmpeg+rtsp推流服务器

根据结果，需要准备（版本随意，但是不能是pre版本）：

```sh
x264
ffmpeg
node
easydarwin
go

```

Sh

编译结果：[ros-arm64-ubuntu18.04编译结果本站下载](https://feater.top/downloads/Applications/EasyDarwin-linux-8.1.0-20043010.tar.gz)

# <a id="i-2"></a>前提

公司的智能车是小R智能科技的，系统为Ubuntu 18.04 bionic，ros系统为ros melodic desktop搭配NVIDIA jetson nano显卡，安装的系统包为jetson-nano-sd-r32.1-2019-03-18（`https://developer.nvidia.com/embedded/downloads`）

# <a id="i-3"></a>准备

x264 `https://www.videolan.org/developers/x264.html`

ffmpeg `http://ffmpeg.org/download.html`

node `https://nodejs.org/zh-cn/download/current/`

go `https://golang.google.cn/dl/`

easydarwin `https://github.com/easydarwin/easydarwin`

# <a id="i-4"></a>编译安装

## <a id="x264"></a>x264

下载源代码，解压，然后：

```bash
./configure
make -jn
sudo make install

```

Bash

会默认安装到`/usr/local/lib`中

## <a id="ffmpeg"></a>ffmpeg

下载源代码，解压，编译：

```bash
./configure --enable-shared --enable-x264 --enable-gpl
make -jn
sudo make install

```

Bash

会默认安装到`/usr/local/`文件夹中。

刚开始我只有–enable-shared 最后运行时出现错误：“ffmpeg \[h264_v4lm2m @ 0x556643f480\] Could not find a valid device”，就是因为没有添加–enable-x264导致的，添加x264需要添加–enable-gpl。

## <a id="go"></a>go

下载arm版linux包（`https://dl.google.com/go/go1.13.10.linux-arm64.tar.gz`），并且解压到`/usr/local/`文件夹下，设置环境变量（.bashrc或者.zshrc）：

```bash
export GOROOT=/usr/local/go
export GOPATH=HOME/go
export PATH=GOROOT/bin:GOPATH:/bin:PATH

```

Bash

GOROOT是go官方软件的位置，GOPATH是go用户的自定义位置（默认用户软件安装位置）

安装软件包：

```bash
go get -u -v github.com/rainycape/dl
go get -u -v github.com/kardianos/govendor
go get -u -v github.com/penggy/gobuild
go get -u -v github.com/Bowery/prompt
go get -u -v github.com/common-nighthawk/go-figure

```

Bash

会自动安装到$GOPATH目录下，并且自动编译。

# <a id="node"></a>node

从官方网站下载arm版的node，版本随意推荐最新的。

然后解压到`/usr/local/`文件夹下就可以了，同时设置环境变量`/usr/local/bin`、`/usr/local/include/`、`/usr/local/lib`等等。

注意，不能是pre版本，否则后面会出错。

安装软件包

```bash
npm i -g @penggy/pack
npm i -g cross-dev --save-dev
npm i -g rimraf
npm i -g apidoc

```

Bash

`-g`表示安装到系统目录，需要`sudo`。默认安装到当前目录。

# <a id="easydarwin"></a>easydarwin

下载（安装官方说明）：

```bash
cd $GOPATH/src/github.com
mkdir EasyDarwin
cd EasyDarwin
git clone https://github.com/EasyDarwin/EasyDarwin.git --depth=1 EasyDarwin
cd EasyDarwin

```

Bash

先到`web_src`安装依赖，然后编译前端：

```bash
cd web_src
npm i
cd ..
npm run build:www

```

Bash

然后编译软件：

```bash
npm run build:lin

```

Bash

打包：

```bash
npm run build:lin
pack tar

```

Bash

可执行文件下载地址：

# <a id="i-5"></a>测试

解压打包后的可执行文件，然后：

```bash
sudo sh start.sh

```

Bash

第一次执行需要将服务复制到系统服务目录，需要sudo权限。

启动服务：

```bash
$ sudo sh start.sh
[sudo] password for xrrobot: 
[EasyDarwin] 2020/04/30 11:48:22 main.go:186: git commit code:8637f73
[EasyDarwin] 2020/04/30 11:48:22 main.go:187: build date:2020-04-30 10:10:08
  _____                         ____                              _
 | ____|   __ _   ___   _   _  |  _ \    __ _   _ __  __      __ (_)  _ __
 |  _|    / _` | / __| | | | | | | | |  / _` | | '__| \ \ /\ / / | | | '_ \
 | |___  | (_| | \__ \ | |_| | | |_| | | (_| | | |     \ V  V /  | | | | | |
 |_____|  \__,_| |___/  \__, | |____/   \__,_| |_|      \_/\_/   |_| |_| |_|
                        |___/
[EasyDarwin] 2020/04/30 11:48:22 main.go:214: EasyDarwin_Service install ...
[EasyDarwin] 2020/04/30 11:48:22 main.go:216: Failed to install EasyDarwin_Service: Init already exists: /etc/systemd/system/EasyDarwin_Service.service
[EasyDarwin] 2020/04/30 11:48:22 utils.go:234: Press any to exit
[EasyDarwin] 2020/04/30 11:48:27 main.go:186: git commit code:8637f73
[EasyDarwin] 2020/04/30 11:48:27 main.go:187: build date:2020-04-30 10:10:08
  _____                         ____                              _
 | ____|   __ _   ___   _   _  |  _ \    __ _   _ __  __      __ (_)  _ __
 |  _|    / _` | / __| | | | | | | | |  / _` | | '__| \ \ /\ / / | | | '_ \
 | |___  | (_| | \__ \ | |_| | | |_| | | (_| | | |     \ V  V /  | | | | | |
 |_____|  \__,_| |___/  \__, | |____/   \__,_| |_|      \_/\_/   |_| |_| |_|
                        |___/
[EasyDarwin] 2020/04/30 11:48:27 main.go:214: EasyDarwin_Service start ...
[EasyDarwin] 2020/04/30 11:48:27 main.go:219: EasyDarwin_Service start ok

```

Bash

服务界面，网址：`http://localhost:10008`

![](http://cdn.feater.top/easydarwin%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%95%8C%E9%9D%A2.png?imageMogr2/blur/1x0/quality/75|watermark/2/text/aHR0cHM6Ly9mZWF0ZXIudG9w/font/5a6L5L2T/fontsize/1400/fill/I0Q5MTQxNA==/dissolve/100/gravity/SouthWest/dx/10/dy/10)

服务器版本信息：

![](http://cdn.feater.top/easydarwin%E7%89%88%E6%9C%AC%E4%BF%A1%E6%81%AF.png?imageMogr2/blur/1x0/quality/75|watermark/2/text/aHR0cHM6Ly9mZWF0ZXIudG9w/font/5a6L5L2T/fontsize/1400/fill/I0Q5MTQxNA==/dissolve/100/gravity/SouthWest/dx/10/dy/10)

使用FFmpeg推流，插入usb摄像头，linux会生成一个/dev/video0的文件。

```bash
ffmpeg -re i /dev/video0 -rtsp_transport tcp -vcodec h264 -f rtsp rtsp://localhost/test

```

Bash

推流结果：

![](http://cdn.feater.top/FFmpeg%E6%8E%A8%E6%B5%81%E8%BE%93%E5%87%BA.png?imageMogr2/blur/1x0/quality/75|watermark/2/text/aHR0cHM6Ly9mZWF0ZXIudG9w/font/5a6L5L2T/fontsize/1400/fill/I0Q5MTQxNA==/dissolve/100/gravity/SouthWest/dx/10/dy/10)

easydarwin推流界面：

<img width="1092" height="1092" src=":/cdfda4cc0cf34659a9cbb311ef7e3b92"/>

FFmpeg取流：

<img width="1092" height="1092" src=":/cdfda4cc0cf34659a9cbb311ef7e3b92"/>

十分卡，考虑撤销缓存和使用cuda加速。

[FFmpeg](https://feater.top/tag/ffmpeg/)

## 文章导航

[Previous Post<br>### Linux下C51开发9：16×16点阵](https://feater.top/series/c51/226/)

[Next Post<br>### Ubuntu开启MySQL的MySQL Query Cache](https://feater.top/linux/283/)

### Leave a Reply

<a id="email-notes"></a>电子邮件地址不会被公开。

评论

姓名

电子邮件

站点

在此浏览器中保存我的名字、电邮和网站。

Protected with [<img width="1092" height="1092" src=":/cdfda4cc0cf34659a9cbb311ef7e3b92"/>](https://www.ip-finder.me)[IP Blacklist Cloud](https://www.ip-finder.me "IP Blacklist Cloud")

|     |     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- | --- |2020年九月
| 一   | 二   | 三   | 四   | 五   | 六   | 日   |
| --- | --- | --- | --- | --- | --- | --- |
|     | 1   | 2   | 3   | [4](https://feater.top/date/2020/09/04/) | 5   | [6](https://feater.top/date/2020/09/06/) |
| [7](https://feater.top/date/2020/09/07/) | 8   | [9](https://feater.top/date/2020/09/09/) | [10](https://feater.top/date/2020/09/10/) | 11  | [12](https://feater.top/date/2020/09/12/) | [13](https://feater.top/date/2020/09/13/) |
| 14  | 15  | 16  | [17](https://feater.top/date/2020/09/17/) | 18  | [19](https://feater.top/date/2020/09/19/) | [20](https://feater.top/date/2020/09/20/) |
| 21  | 22  | 23  | [24](https://feater.top/date/2020/09/24/) | [25](https://feater.top/date/2020/09/25/) | 26  | 27  |
| 28  | 29  | 30  |     |     |     |     |

[« 8月](https://feater.top/date/2020/08/)  

### 标签

[C++](https://feater.top/tag/cpp/) [C51](https://feater.top/tag/c51/) [CentOS](https://feater.top/tag/centos/) [FFmpeg](https://feater.top/tag/ffmpeg/) [Linux](https://feater.top/tag/linux/) [MySQL](https://feater.top/tag/mysql/) [OpenCV](https://feater.top/tag/opencv/) [OpenGL](https://feater.top/tag/opengl/) [PDF](https://feater.top/tag/pdf/) [Qt](https://feater.top/tag/qt/) [合集](https://feater.top/tag/%e5%90%88%e9%9b%86/) [破解](https://feater.top/tag/%e7%a0%b4%e8%a7%a3/) [说明](https://feater.top/tag/%e8%af%b4%e6%98%8e/)

### 近期评论

- [JackeyLea](https://feater.top)发表在《[关于本站](https://feater.top/about/8/#comment-107)》
- 匿名发表在《[Adobe Acrobat Pro DC 2020 破解/注册机/激活](https://feater.top/crack/664/#comment-106)》
- [regrex](https://www.dcdapp.com/)发表在《[关于本站](https://feater.top/about/8/#comment-105)》
- 不要人夸名字好发表在《[关于本站](https://feater.top/about/8/#comment-103)》
- 匿名发表在《[计算机系统基础（第2版）袁春风编著 课后习题参考答案](https://feater.top/ebooks/36/#comment-101)》

### 近期文章

- [OpenCV4入门系列教程21：Qt格式的按钮](https://feater.top/series/opencv/1425/) 2020年9月25日
- [OpenCV4入门系列教程18：图像掩膜mask](https://feater.top/series/opencv/1414/) 2020年9月25日
- [OpenCV4入门系列教程17：图像的数学操作bitwise（和差积）](https://feater.top/series/opencv/1412/) 2020年9月24日
- [OpenCV4入门系列教程测试2：像素插值实现缩放](https://feater.top/series/opencv/1410/) 2020年9月24日
- [OpenCV4入门系列教程12：键盘输入waitKey()](https://feater.top/series/opencv/1394/) 2020年9月24日

- 在线用户: 0
- 今日访问: 72
- 今日访客: 41
- 昨日访问: 394
- 昨日访客: 181
- 过去7天的访问量: 3,850
- 过去30天的访问量: 11,307
- 过去365天的访问量: 46,148
- 累计访问: 46,148
- 累计访客: 19,235
- 总浏览量: 16
- 搜索引擎引用次数: 2,964
- 总计文章: 183
- 评论总数: 49
- 最后发表日期: 2020年9月26日

WordPress Theme: Maxwell by [ThemeZee](https://themezee.com/).