MQTT与Android_手写我对你的温柔-CSDN博客_mqtt 安卓

# MQTT与Android

<img width="36" height="32" src="../_resources/3ef35230cb0045d7bb987fc4a823ab42.png"/>

[我的城市下着雨](https://yinleilei.blog.csdn.net) 2020-02-21 16:34:00 <img width="24" height="24" src="../_resources/14b04f45a2454cf8a61c6c18c87c2421.png"/>824 <a id="blog_detail_zk_collection"></a><img width="20" height="20" src="../_resources/4e2dc48f74da492fb2af33a7bc91d534.png"/>收藏 5 

分类专栏： [物联网](https://blog.csdn.net/qq_39969226/category_9622300.html) [Android](https://blog.csdn.net/qq_39969226/category_8624546.html)

版权

接上文：[MQTT协议入门](https://blog.csdn.net/qq_39969226/article/details/103712767?ops_request_misc=%7B%22request_id%22%3A%22158226741319724846428303%22%2C%22scm%22%3A%2220140713.130056874..%22%7D&request_id=158226741319724846428303&biz_id=0&utm_source=distribute.pc_search_result.none-task)

首先应部署EMQX到公网上以便后续的访问操作。

<img width="962" height="243" src=":/9f82962d62bd4b079a611d7dec5f7384"/>

等会儿的结果信息可以在这里查看。

* * *

### <a id="t0"></a><a id="t0"></a>MQTT回顾：

![](:/b440d1a5823a42fc8b2d266fa322ebdc)

MQTT协议有三种身份：发布者、代理、订阅者，发布者和订阅者都为客户端，代理为服务器，同时消息的发布者也可以是订阅者（为了节约内存和流量发布者和订阅者一般都会定义在一起）。
MQTT传输的消息分为主题（Topic，可理解为消息的类型，订阅者订阅后，就会收到该主题的消息内容（payload））和负载（payload，可以理解为消息的内容）两部分。

JAVA中：阿里专门针对Android客户端的实现方式MqttAndroidClient

> ```
> implementation 'org.eclipse.paho:org.eclipse.paho.client.mqttv3:1.2.0'
> ```

* * *

<img width="962" height="486" src="../_resources/16a5c6b416cf439e8deec6c23bcb9222.png"/>

这里有个简单的UI，

先想清楚做什么：

> Android端充当订阅者，订阅消息主题，然后MQTT.fx工具模拟发布者发布消息,让Android端接收消息。
> 
> 然后反过来Android端充当发布者发布消息。然后MQTT.fx工具模拟订阅者，订阅消息主题，收到来自Android端的消息。

总体的步骤：

![](:/aa25bc49843c4858ae8c684a5f7724c2)

* * *

### <a id="t1"></a><a id="t1"></a>1.Android端充当订阅者，订阅消息主题，然后MQTT.fx工具模拟发布者发布消息,让Android端接收消息。

![](:/96aaec1e3be94de2aaf848d49d7bb679)

程序启动，android端![](:/e8cd7872ba4d4ddd9026c5264ef345f3)连接、订阅主题：

<img width="962" height="205" src="../_resources/256e1b3b5ab144b38e2704830d663932.png"/>

并设置了消息的回调：

<img width="962" height="502" src="../_resources/002e388c7fec4d63927d69bac02a90b9.png"/>

 ，然后是MQTT.fx:

<img width="962" height="706" src="../_resources/4df38c2bb75648d3907fcacd8118a904.png"/>

<img width="962" height="166" src=":/b578e81c5787429093546a081ab52b4f"/> 发送者向订阅者Android发送了1条消息。

![](0d01f54e7472472bab1a7d550692bf81.png)

客户端根据回调处理消息结果并进行UI展示：

<img width="962" height="109" src=":/90cc5d6aec3542129a161f9893d43039"/>

* * *

### <a id="t2"></a><a id="t2"></a>2.Android端充当发布者发布消息。然后MQTT.fx工具模拟订阅者，订阅消息主题，收到来自Android端的消息。

![](:/de17e726677e4dac8ce293e8fcb36a4f)

<img width="962" height="119" src=":/94da8a7112ff47cb9dfb531331144519"/>

<img width="962" height="241" src="../_resources/3cf4d0efc78446a9ab486586fbd51030.png"/>

这里是客户端推送给另一端：

<img width="962" height="417" src="../_resources/1e11eadd500a47429171742a1b9e1d38.png"/>

订阅了android端的主题后，当android端点击按钮发送一条消息后，订阅者就可以接收消息。

* * *

### <a id="t3"></a><a id="t3"></a>全部源码：

```


1.  package com.yinlei.mqtt_project
    

3.  import androidx.appcompat.app.AppCompatActivity
    
4.  import android.os.Bundle
    
5.  import android.os.Handler
    
6.  import android.os.Message
    
7.  import android.util.Log
    
8.  import android.widget.Toast
    
9.  import kotlinx.android.synthetic.main.activity_main.*
    
10. import org.eclipse.paho.client.mqttv3.*
    
11. import org.eclipse.paho.client.mqttv3.persist.MemoryPersistence
    
12. import java.util.concurrent.Executors
    
13. import java.util.concurrent.ScheduledExecutorService
    
14. import java.util.concurrent.TimeUnit
    
15. import kotlin.Exception
    
16. import kotlin.concurrent.thread
    

18. /**
    
19.  * 1.连接成功
    
20.  * 2. 开始订阅topic
    
21.  * 3.拿到数据做逻辑、UI处理
    
22.  * 4.发布消息到指定topic
    
23.  * 5. 客户端发布消息给硬件。
    
24.  * 6.硬件得到消息并做相应的处理。
    
25.  */
    
26. class MainActivity : AppCompatActivity() {
    

29.     private val host ="填写自己的broker"
    
30.     private val userNames = "android"//用户名
    
31.     private val passWord = "android"//密码
    
32.     private val mqtt_id = "1099129793"//客户端id
    
33.     private val mqtt_sub_topic = "1099129793"//主题
    
34.     private val mqtt_pub_topic = "YL1099129793"//发布的主题topic
    
35.     private val scheduler: ScheduledExecutorService by lazy {
    
36.         Executors.newSingleThreadScheduledExecutor()
    
37.     }
    

40.     private val client: MqttClient by lazy{
    
41.         //host为主机名，test为clientid即连接MQTT的客户端ID，一般以客户端唯一标识符表示，MemoryPersistence设置clientid的保存形式，默认为以内存保存
    
42.          MqttClient(host, mqtt_id, MemoryPersistence())
    
43.     }
    
44.     private  val options: MqttConnectOptions by lazy {
    
45.         //MQTT的连接设置
    
46.          MqttConnectOptions()
    
47.     }
    
48.     private lateinit var  handler: Handler
    

50.     override fun onCreate(savedInstanceState: Bundle?) {
    
51.         super.onCreate(savedInstanceState)
    
52.         setContentView(R.layout.activity_main)
    

54.         btn_1.setOnClickListener {
    
55.             Toast.makeText(this@MainActivity,"Hello MQTT", Toast.LENGTH_SHORT).show()
    
56.         }
    
57.         image_1.setOnClickListener {
    
58.             Toast.makeText(this@MainActivity,"图片1", Toast.LENGTH_SHORT).show()
    
59.             PublishMessagepWrapper(mqtt_pub_topic, "Android客户端发布的消息")
    
60.         }
    

63.         MqttInit()
    
64.         startReconnect()
    

66.         handler = Handler{
    
67.             when(it.what){
    
68.                 1 ->{
    
69.                     Log.d("yinlei","开机校验更新回传")
    
70.                 }
    
71.                 2 ->{
    
72.                     Log.d("yinlei","反馈回传")
    
73.                 }
    
74.                 3 -> {
    
75.                     Log.d("yinlei","MQTT 收到消息回传   UTF8Buffer msg=new UTF8Buffer(object.toString());")
    
76.                     Toast.makeText(this@MainActivity,it.obj.toString(), Toast.LENGTH_SHORT).show()
    
77.                     text_test.text = it.obj.toString()
    
78.                 }
    
79.                 30 -> {
    
80.                     Log.d("yinlei","连接失败   UTF8Buffer msg=new UTF8Buffer(object.toString());")
    
81.                     Toast.makeText(this@MainActivity,"连接失败", Toast.LENGTH_SHORT).show()
    
82.                 }
    
83.                 31 -> {
    
84.                     Log.d("yinlei","连接成功")
    
85.                     Toast.makeText(this@MainActivity,"连接成功", Toast.LENGTH_SHORT).show()
    
86.                     try {
    
87.                         client.subscribe(mqtt_sub_topic,1) //订阅主题
    
88.                     }catch (e: Exception){
    
89.                         e.printStackTrace()
    
90.                     }
    
91.                 }
    
92.                 else -> {
    
93.                     Log.d("yinlei","未知错误")
    
94.                 }
    
95.             }
    
96.             false
    
97.         }
    

99.     }
    

102.    private fun MqttInit() {
    
103.        try{
    
104.            with(options){
    
105.                //设置是否清空session,这里如果设置为false表示服务器会保留客户端的连接记录，这里设置为true表示每次连接到服务器都以新的身份连接
    
106.                isCleanSession = false
    
107.                //设置连接的用户名
    
108.                userName = userNames
    
109.                //设置连接的密码
    
110.                password = passWord.toCharArray()
    
111.                // 设置超时时间 单位为秒
    
112.                connectionTimeout = 10
    
113.                // 设置会话心跳时间 单位为秒 服务器会每隔1.5*20秒的时间向客户端发送个消息判断客户端是否在线，但这个方法并没有重连的机制
    
114.                keepAliveInterval = 20
    
115.            }
    

117.            //设置回调
    
118.            client.setCallback(object : MqttCallback {
    
119.                override fun messageArrived(topic: String?, message: MqttMessage?) {
    
120.                    //subscribe后得到的消息会执行到这里面
    
121.                    Log.d("yinlei","messageArrived")
    
122.                    val msg = Message()
    
123.                    msg.what = 3
    
124.                    msg.obj = "${topic}___${message.toString()}"
    
125.                    handler.sendMessage(msg) //发送消息
    
126.                }
    

128.                override fun connectionLost(cause: Throwable?) {
    
129.                    //连接丢失后，一般在这里面进行重连
    
130.                    Log.d("yinlei","connectionLost")
    

132.                }
    

134.                override fun deliveryComplete(token: IMqttDeliveryToken?) {
    
135.                    //publish后会执行到这里
    
136.                    Log.d("yinlei","deliveryComplete")
    
137.                }
    

139.            })
    
140.        }catch (e: Exception){
    
141.            e.printStackTrace()
    
142.        }
    
143.    }
    

146.    private fun startReconnect() {
    
147.        scheduler.scheduleAtFixedRate({
    
148.            if (!client.isConnected){//下线了
    
149.                Mqtt_connect()
    
150.            }
    
151.        }, 0 * 1000, 10 * 1000, TimeUnit.MILLISECONDS)
    

153.    }
    

155.    private fun Mqtt_connect(){
    
156.        thread {
    
157.            try{
    
158.               if (!client.isConnected){
    
159.                   //还未连接
    
160.                   client.connect(options)
    
161.                   val msg = Message().apply { what = 31 }
    
162.                   handler.sendMessage(msg)
    
163.               }
    
164.            }catch (e: Exception){
    
165.                e.printStackTrace()
    
166.                val msg = Message().apply { what = 30 }
    
167.                handler.sendMessage(msg)
    
168.            }
    
169.        }
    
170.    }
    

172.    private fun PublishMessagepWrapper(topic: String, message2: String){
    
173.        if (!client.isConnected){
    
174.            return
    
175.        }
    
176.        val message = MqttMessage().apply { payload = message2.toByteArray() }
    
177.        try {
    
178.            client.publish(topic, message)//客户端发布消息主题
    
179.        }catch (e: MqttException){
    
180.            e.printStackTrace()
    
181.        }
    

183.    }
    

186.}
    


```

* * *

### <a id="t4"></a><a id="t4"></a>MQTT.fx的简单介绍：

![](https://img-blog.csdnimg.cn/20200221163521491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTY5MjI2,size_16,color_FFFFFF,t_70)

<img width="962" height="430" src="../_resources/0308a603d191494fbe653afb7c502714.png"/>

<img width="962" height="462" src="../_resources/0e2f299739d1401fabd680b8832fb4ee.png"/>

![](https://img-blog.csdnimg.cn/20200221163545934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTY5MjI2,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020022116355489.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTY5MjI2,size_16,color_FFFFFF,t_70)

<img width="962" height="511" src=":/8d996e23fece49828d32a07e90f6859e"/>

<img width="962" height="237" src=":/be3d7a41400645428d604f852a79b35e"/>

显示推荐内容

-  <img width="22" height="22" src="../_resources/03e7cd22f1264266afedbd2882aa6a04.png"/> <a id="is-like-span"></a>点赞
- [<img width="22" height="22" src="../_resources/31c683ed2b3d461f8dac42ede734e938.png"/>评论 4](#commentBox) 
- <img width="22" height="22" src=":/c04c5ff387284daf88b1107aa3729593"/>分享
- <img width="22" height="22" src="../_resources/4e2dc48f74da492fb2af33a7bc91d534.png"/><a id="is-collection"></a>收藏 <a id="get-collection"></a>5 
- <a id="toolreward"></a><img width="22" height="22" src=":/e92a711b981c4de2b802e40dd124ffd7"/>打赏
- <img width="22" height="22" src=":/ebb8925b8eae46e88501410ae91064c0"/>举报
- 订阅博主
- 关注
- 一键三连
    

<a id="commentBox"></a>

<a id="commentsedit"></a>

<img width="30" height="30" src="../_resources/22359c5997d249f798c9b5f61d6594ba.png"/>

<img width="18" height="18" src=":/e32e05ba309e40c9ad20cd3b2bf2ddd2"/>

<a id="comments"></a>

- [<img width="24" height="24" src=":/c42230c16c004dd1b1f1b0e99dcedce4"/>](https://blog.csdn.net/qq_38042345)
    
    [日途暮远](https://blog.csdn.net/qq_38042345):楼主有github地址吗10 月前回复<img width="16" height="6" src=":/f35f70f1c58b49818ee57ea7cc8455b5"/>
    
    <img width="16" height="16" src="../_resources/2de07cfb37c34520a29b5c86e5bf4aa0.png"/>
    
- - [![qq_39969226](:/bd3c6378b92846f38ad41300ec00d7f5)](https://blog.csdn.net/qq_39969226)
        
        [码哥![](https://csdnimg.cn/release/blogv2/dist/components/img/commentTagArrowWhite.png "码哥")](https://blog.csdn.net/blogdevteam/article/details/103478461)[我的城市下着雨![](https://csdnimg.cn/release/blogv2/dist/components/img/bloger@2x.png)](https://blog.csdn.net/qq_39969226)回复:代码全都贴出来了啊，还要啥。。。小demo不上传到github10 月前回复<img width="16" height="6" src=":/f35f70f1c58b49818ee57ea7cc8455b5"/>
        
        <img width="16" height="16" src="../_resources/2de07cfb37c34520a29b5c86e5bf4aa0.png"/>
        

- [<img width="24" height="24" src=":/884529f1cfb24504b2d23f45eab73c2f"/>](https://blog.csdn.net/weixin_42446445)
    
    [决胜万里](https://blog.csdn.net/weixin_42446445):都被你学去了，你自己偷偷学的又不教我<img width="20" height="20" src=":/d9dd43654eed45caa475d1040837f2b1"/><img width="20" height="20" src=":/d9dd43654eed45caa475d1040837f2b1"/>1 年前回复<img width="16" height="6" src=":/f35f70f1c58b49818ee57ea7cc8455b5"/>
    
    <img width="16" height="16" src="../_resources/2de07cfb37c34520a29b5c86e5bf4aa0.png"/>
    
- - [![qq_39969226](:/bd3c6378b92846f38ad41300ec00d7f5)](https://blog.csdn.net/qq_39969226)
        
        [码哥![](https://csdnimg.cn/release/blogv2/dist/components/img/commentTagArrowWhite.png "码哥")](https://blog.csdn.net/blogdevteam/article/details/103478461)[我的城市下着雨![](https://csdnimg.cn/release/blogv2/dist/components/img/bloger@2x.png)](https://blog.csdn.net/qq_39969226)回复:不应该你教我?1 年前回复<img width="16" height="6" src=":/f35f70f1c58b49818ee57ea7cc8455b5"/>
        
        <img width="16" height="16" src="../_resources/2de07cfb37c34520a29b5c86e5bf4aa0.png"/>
        

### 目录

1.  [MQTT回顾：](#t0)
2.  [1.Android端充当订阅者，订阅消息主题，然后MQTT.fx工具模拟发布者发布消息,让Android端接收消息。](#t1)
3.  [2.Android端充当发布者发布消息。然后MQTT.fx工具模拟订阅者，订阅消息主题，收到来自Android端的消息。](#t2)
4.  [全部源码：](#t3)
5.  [MQTT.fx的简单介绍：](#t4)

 <img width="24" height="24" src="../_resources/2fad84a521644db5846fcd7271e1aaae.png"/> <img width="24" height="24" src=":/c613908c47c0426ea58ba12b5ae51c4e"/>举报 <img width="24" height="24" src=":/9f8a29e5b3174740b42c9957e7a37d50"/>