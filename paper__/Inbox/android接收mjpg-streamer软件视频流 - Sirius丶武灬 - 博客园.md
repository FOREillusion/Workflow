android接收mjpg-streamer软件视频流 - Sirius丶武灬 - 博客园

<a id="top"></a>

- [<img width="76" height="28" src=":/809626816da548d38b21efaf41ddb950"/>](https://www.cnblogs.com/ "开发者的网上家园")
- [首页](https://www.cnblogs.com/)
- [新闻](https://news.cnblogs.com/)
- [博问](https://q.cnblogs.com/)
- <a id="nav_brandzone"></a>[专区](https://brands.cnblogs.com/)
- [闪存](https://ing.cnblogs.com/)
- [班级](https://edu.cnblogs.com/)

- [注册](https://account.cnblogs.com/signup/) 登录

<a id="Header1_HeaderTitle"></a>[输过败过不曾怕过](https://www.cnblogs.com/sirius-swu/)

积硅步以至千里，积懈怠以致深渊。

- <a id="blog_nav_sitehome"></a>[博客园](https://www.cnblogs.com/)
- <a id="blog_nav_myhome"></a>[首页](https://www.cnblogs.com/sirius-swu/)
- <a id="blog_nav_newpost"></a>[新随笔](https://i.cnblogs.com/EditPosts.aspx?opt=1)
- <a id="blog_nav_contact"></a>[联系](https://msg.cnblogs.com/send/Sirius%E4%B8%B6%E6%AD%A6%E7%81%AC)
- <a id="blog_nav_rss"></a>订阅
- <a id="blog_nav_admin"></a>[管理](https://i.cnblogs.com/)

# <a id="cb_post_title_url"></a>[android接收mjpg-streamer软件视频流](https://www.cnblogs.com/sirius-swu/p/6815840.html)

\[代码\]主要实现代码

|     |     |
| --- | --- |
| 1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26<br><br>27<br><br>28<br><br>29<br><br>30<br><br>31<br><br>32<br><br>33<br><br>34<br><br>35<br><br>36<br><br>37<br><br>38<br><br>39<br><br>40<br><br>41<br><br>42<br><br>43<br><br>44<br><br>45<br><br>46<br><br>47<br><br>48<br><br>49<br><br>50<br><br>51<br><br>52<br><br>53<br><br>54<br><br>55<br><br>56<br><br>57<br><br>58<br><br>59<br><br>60<br><br>61<br><br>62<br><br>63<br><br>64<br><br>65<br><br>66<br><br>67<br><br>68<br><br>69<br><br>70<br><br>71<br><br>72<br><br>73<br><br>74<br><br>75<br><br>76<br><br>77<br><br>78<br><br>79<br><br>80<br><br>81<br><br>82<br><br>83<br><br>84<br><br>85<br><br>86<br><br>87<br><br>88<br><br>89<br><br>90<br><br>91<br><br>92<br><br>93<br><br>94<br><br>95<br><br>96<br><br>97<br><br>98<br><br>99<br><br>100<br><br>101<br><br>102<br><br>103<br><br>104<br><br>105<br><br>106<br><br>107<br><br>108 | `package` `cn.dong.mjpeg;`<br><br>`import` `java.io.InputStream;`<br><br>`import` `java.net.HttpURLConnection;`<br><br>`import` `java.net.URL;`<br><br>`import` `android.app.Activity;`<br><br>`import` `android.content.pm.ActivityInfo;`<br><br>`import` `android.graphics.Bitmap;`<br><br>`import` `android.graphics.BitmapFactory;`<br><br>`import` `android.graphics.Canvas;`<br><br>`import` `android.graphics.Color;`<br><br>`import` `android.graphics.RectF;`<br><br>`import` `android.os.Bundle;`<br><br>`import` `android.view.SurfaceHolder;`<br><br>`import` `android.view.Window;`<br><br>`import` `android.view.SurfaceHolder.Callback;`<br><br>`import` `android.view.SurfaceView;`<br><br>`public` `class` `surface` `extends` `Activity` `implements` `Runnable{`<br><br>`private` `SurfaceHolder holder;   `<br><br>`private` `Thread mythread;   `<br><br>`private` `Canvas canvas;`<br><br>`URL videoUrl;`<br><br>`private` `String url;`<br><br>`private` `int` `w;`<br><br>`private` `int` `h;`<br><br>`HttpURLConnection conn;`<br><br>`Bitmap bmp;`<br><br>`@Override`<br><br>`protected` `void` `onCreate(Bundle savedInstanceState) {`<br><br>`requestWindowFeature(Window.FEATURE_NO_TITLE);`<br><br>`setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);`<br><br>`// TODO Auto-generated method stub`<br><br>`super``.onCreate(savedInstanceState);`<br><br>`setContentView(R.layout.surface);`<br><br>`url = getIntent().getExtras().getString(``"CameraIp"``);`<br><br>`w = getWindowManager().getDefaultDisplay().getWidth();`<br><br>`h = getWindowManager().getDefaultDisplay().getHeight();`<br><br>`SurfaceView surface = (SurfaceView)findViewById(R.id.surface);`<br><br>`surface.setKeepScreenOn(``true``);``// 保持屏幕常亮`<br><br>`mythread =` `new` `Thread(``this``);    `<br><br>`holder = surface.getHolder();`<br><br>`holder.addCallback(``new` `Callback() {`<br><br>`@Override`<br><br>`public` `void` `surfaceDestroyed(SurfaceHolder holder) {`<br><br>`// TODO Auto-generated method stub`<br><br>`}`<br><br>`@Override`<br><br>`public` `void` `surfaceCreated(SurfaceHolder holder) {`<br><br>`// TODO Auto-generated method stub`<br><br>`mythread.start();`<br><br>`}`<br><br>`@Override`<br><br>`public` `void` `surfaceChanged(SurfaceHolder holder,` `int` `format,` `int` `width,`<br><br>`int` `height) {`<br><br>`// TODO Auto-generated method stub`<br><br>`}`<br><br>`});`<br><br>`}`<br><br>`private` `void` `draw() {`<br><br>`// TODO Auto-generated method stub`<br><br>`try` `{   `<br><br>`InputStream inputstream =` `null``;`<br><br>`//创建一个URL对象`<br><br>`//          url = "http://192.168.8.1:8083/?action=snapshot";`<br><br>`videoUrl=``new` `URL(url);   `<br><br>`//利用HttpURLConnection对象从网络中获取网页数据`<br><br>`conn = (HttpURLConnection)videoUrl.openConnection();`<br><br>`//设置输入流`<br><br>`conn.setDoInput(``true``);`<br><br>`//连接`<br><br>`conn.connect();`<br><br>`//得到网络返回的输入流`<br><br>`inputstream = conn.getInputStream();`<br><br>`//创建出一个bitmap`<br><br>`bmp = BitmapFactory.decodeStream(inputstream);`<br><br>`canvas = holder.lockCanvas();`<br><br>`canvas.drawColor(Color.WHITE);`<br><br>`RectF rectf =` `new` `RectF(``0``,` `0``, w, h);`<br><br>`canvas.drawBitmap(bmp,` `null``, rectf,` `null``);`<br><br>`holder.unlockCanvasAndPost(canvas);`<br><br>`//关闭HttpURLConnection连接`<br><br>`conn.disconnect();`<br><br>`}` `catch` `(Exception ex) {   `<br><br>`}` `finally` `{     `<br><br>`}`<br><br>`}`<br><br>`@Override`<br><br>`public` `void` `run() {`<br><br>`// TODO Auto-generated method stub`<br><br>`while``(``true``){`<br><br>`draw();`<br><br>`}`<br><br>`}`<br><br>`}` |

<a id="green_channel_digg"></a>好文要顶 <a id="green_channel_follow"></a>关注我 <a id="green_channel_favorite"></a>收藏该文 <a id="green_channel_weibo"></a>![](46eea38180374dfd833cdefb886c83ab.png)<a id="green_channel_wechat"></a><img width="24" height="24" src="../_resources/54e163a81fd94d41b26dbde077a961aa.png"/>

[![](:/e7a7f726da2a477ebf24b10180145c17)](https://home.cnblogs.com/u/sirius-swu/)

[Sirius丶武灬](https://home.cnblogs.com/u/sirius-swu/)
[关注 \- 4](https://home.cnblogs.com/u/sirius-swu/followees/)
[粉丝 \- 5](https://home.cnblogs.com/u/sirius-swu/followers/)

+加关注

<a id="digg_count"></a>0

<a id="bury_count"></a>0

[«](https://www.cnblogs.com/sirius-swu/p/6746290.html) 上一篇： [前端开发之旅\- 移动端HTML5实现文件上传](https://www.cnblogs.com/sirius-swu/p/6746290.html "发布于 2017-04-21 22:02")
[»](https://www.cnblogs.com/sirius-swu/p/6823634.html) 下一篇： [基于云计算感应器的智能盆栽监测系统](https://www.cnblogs.com/sirius-swu/p/6823634.html "发布于 2017-05-08 10:06")

posted @ <a id="post-date"></a>2017-05-06 10:00  [Sirius丶武灬](https://www.cnblogs.com/sirius-swu/)  阅读(<a id="post_view_count"></a>3317)  评论(<a id="post_comment_count"></a>0)  [编辑](https://i.cnblogs.com/EditPosts.aspx?postid=6815840)  收藏

<a id="!comments"></a>

<a id="commentform"></a>

<a id="lnk_RefreshComments"></a>刷新评论[刷新页面](#)[返回顶部](#top)

登录后才能发表评论，立即 登录 或 注册， [访问](https://www.cnblogs.com/) 网站首页

**AWS免费产品**：
· [如何在AWS上免费构建网站](https://aws.amazon.com/cn/free/webapps/?trk=ba_a134p000006vc6OAAQ&trkCampaign=webapps&sc_channel=ba&sc_campaign=cnblogs&sc_outcome=Acquisition&sc_geo=CHNA&sc_country=CN&sc_publisher=Others)
· [AWS免费云存储解决方案](https://aws.amazon.com/cn/free/storage/?trk=ba_a134p000006vc7JAAQ&trkCampaign=storage&sc_channel=ba&sc_campaign=cnblogs&sc_outcome=Acquisition&sc_geo=CHNA&sc_country=CN&sc_publisher=Others)
· [在AWS上免费构建数据库](https://aws.amazon.com/cn/free/database/?trk=ba_a134p000006vc7YAAQ&trkCampaign=database&sc_channel=ba&sc_campaign=cnblogs&sc_outcome=Acquisition&sc_geo=CHNA&sc_country=CN&sc_publisher=Others)
· [AWS上的免费机器学习](https://aws.amazon.com/cn/free/machine-learning/?trk=ba_a134p000006vc7nAAA&trkCampaign=ML&sc_channel=ba&sc_campaign=cnblogs&sc_outcome=Acquisition&sc_geo=CHNA&sc_country=CN&sc_publisher=Others)

**最新新闻**：
· [Facebook推出主打说唱体验的实验性BARS短视频服务](https://news.cnblogs.com/n/689154/)
· [谷歌为Chrome浏览器带来Live Captions实时翻译功能](https://news.cnblogs.com/n/689153/)
· [家乐福利用IBM区块链技术追踪鸡肉和蔬菜供应](https://news.cnblogs.com/n/689152/)
· [前SolarWinds CEO将 "solarwinds123"密码泄露事件归咎于实习生](https://news.cnblogs.com/n/689150/)
· [利用微信朋友圈为赌博网站推送广告获利千万 21人获刑](https://news.cnblogs.com/n/689149/)
» [更多新闻...](https://news.cnblogs.com/ "IT 新闻")

### 公告

昵称： [Sirius丶武灬](https://home.cnblogs.com/u/sirius-swu/)
园龄： [5年2个月](https://home.cnblogs.com/u/sirius-swu/ "入园时间：2015-12-27")
粉丝： [5](https://home.cnblogs.com/u/sirius-swu/followers/)
关注： [4](https://home.cnblogs.com/u/sirius-swu/followees/)

+加关注

|     |     |     |
| --- | --- | --- |
| <   | 2021年2月 | >   |

日一二三四五六311234567891011121314151617181920212223242526272812345678910111213

### 搜索

### 常用链接

- [我的随笔](https://www.cnblogs.com/sirius-swu/p/ "我的博客的随笔列表")
- [我的评论](https://www.cnblogs.com/sirius-swu/MyComments.html "我的发表过的评论列表")
- [我的参与](https://www.cnblogs.com/sirius-swu/OtherPosts.html "我评论过的随笔列表")
- [最新评论](https://www.cnblogs.com/sirius-swu/RecentComments.html "我的博客的评论列表")
- [我的标签](https://www.cnblogs.com/sirius-swu/tag/ "我的博客的标签列表")

### 随笔档案

- [2017年7月(1)](https://www.cnblogs.com/sirius-swu/archive/2017/07.html)
- [2017年6月(9)](https://www.cnblogs.com/sirius-swu/archive/2017/06.html)
- [2017年5月(11)](https://www.cnblogs.com/sirius-swu/archive/2017/05.html)
- [2017年4月(15)](https://www.cnblogs.com/sirius-swu/archive/2017/04.html)
- [2017年3月(7)](https://www.cnblogs.com/sirius-swu/archive/2017/03.html)

### 最新评论

- [1\. Re:QT开发之旅-Udp聊天室编程](https://www.cnblogs.com/sirius-swu/p/6919924.html)
- 请问可以发一份源码吗？课程需要，想参考一下，1172903346@qq.com
    
- --Riverlll
- [2\. Re:Android系统架构](https://www.cnblogs.com/sirius-swu/p/6616020.html)
- 支持支持
- --牛腩

### 阅读排行榜

- [1\. 机器学习-随机梯度下降（Stochastic gradient descent）和 批量梯度下降（Batch gradient descent ）(16244)](https://www.cnblogs.com/sirius-swu/p/6932583.html)
- [2\. 前端开发之旅- 移动端HTML5实现文件上传(4054)](https://www.cnblogs.com/sirius-swu/p/6746290.html)
- [3\. android接收mjpg-streamer软件视频流(3317)](https://www.cnblogs.com/sirius-swu/p/6815840.html)
- [4\. Raspberry Pi开发之旅-光照强度检测（BH1750）(3063)](https://www.cnblogs.com/sirius-swu/p/6682746.html)
- [5\. 前端开发之旅-zopim在线即时聊天客服(2588)](https://www.cnblogs.com/sirius-swu/p/6713655.html)

### 评论排行榜

- [1\. QT开发之旅-Udp聊天室编程(1)](https://www.cnblogs.com/sirius-swu/p/6919924.html)
- [2\. Android系统架构(1)](https://www.cnblogs.com/sirius-swu/p/6616020.html)

### 推荐排行榜

- [1\. 机器学习-随机梯度下降（Stochastic gradient descent）和 批量梯度下降（Batch gradient descent ）(3)](https://www.cnblogs.com/sirius-swu/p/6932583.html)
- [2\. 机器学习 第一章-总结(1)](https://www.cnblogs.com/sirius-swu/p/6890005.html)
- [3\. 基于云计算感应器的智能盆栽监测系统(1)](https://www.cnblogs.com/sirius-swu/p/6823634.html)

Copyright © 2021 Sirius丶武灬
<a id="poweredby"></a>Powered by .NET 5.0 on Kubernetes