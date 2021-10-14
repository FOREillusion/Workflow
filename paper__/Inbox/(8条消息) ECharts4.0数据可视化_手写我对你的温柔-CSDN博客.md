(8条消息) ECharts4.0数据可视化_手写我对你的温柔-CSDN博客



# ECharts4.0数据可视化

<img width="36" height="32" src="../_resources/47cee5d0630041d1a1a07bd7d26cbfe8.png"/>

[我的城市下着雨](https://yinleilei.blog.csdn.net) 2020-05-14 20:33:59 <img width="24" height="24" src="../_resources/35cc307af01f4337b6836d66f57cb52f.png"/>400 <a id="blog_detail_zk_collection"></a><img width="20" height="20" src=":/8a858596d94f4494b08d50ffd69cb024"/>收藏 2 

分类专栏： [大前端](https://blog.csdn.net/qq_39969226/category_9126790.html)

版权

绘图需求：SVG 和 Canvas

基于SVG和Canvas，百度推出了Echarts图形库。

(更多api用法可查看官网文档)

* * *

### <a id="t0"></a><a id="t0"></a>浏览器绘图原理：

编写的前端代码最终都将经过浏览器渲染成一个页面。浏览器绘图通过HTML，还可以通过Canvas和SVG的方式来绘图。实际上，HTML、CSS实际都是针对浏览器绘图做出一些指令。

> （关于SVG和Canvas的具体用法，可查看我的其他博文）

**canvas绘图**：通过canvas创建的画布内容允许自己去对内容绘制。

<img width="962" height="244" src=":/f809150fcc1048f79d4a1fd43b528b76"/>

canvas只创建，内容需要我们填充：

<img width="962" height="258" src="../_resources/6f497bee5c55427db375775409ebd7d2.png"/>

canvas这些中的元素是无法被选中的，和dom不同。如果做canvas动画就需要不停的刷新canvas画布。

canvas在很多语言和编程方向中道理都是一致的，例如android中的canvas和flutter等：

<img width="962" height="606" src="../_resources/6281df7d61ca465ca84218e9df0cabaa.png"/>

**SVG绘图：表示矢量绘图。**

**通过**html标签进行绘图。canvas绘图无法被选中。SVG绘制的可以被选中。

<img width="962" height="394" src=":/858968e9ce7849078abe02ba4eddc328"/>

放大图，canvas会有锯齿失真，而SVG不会。

Canvas更适合做高性能的动画渲染，Echarts支持canvas和svg。

为了减少canvas失真，一般：

- 开启抗锯齿
- 对图形的分辨率做的更大一些，然后对其进行缩放

* * *

### <a id="t1"></a><a id="t1"></a>Echarts：

可以解析为canvas和svg版本。

<img width="962" height="452" src="../_resources/723d485aaa654dbcbf10677de5c3b9b3.png"/>

1.简单的案例体验：

<img width="962" height="343" src="../_resources/02f539be6d4c49c8a12ef7f4b5a31383.png"/>

引入后编写代码：

<img width="962" height="643" src="../_resources/56e41558f7e440278819555ce4cedc3b.png"/>

<img width="962" height="561" src=":/eac0e8793edf4cc5b117e994490cb3f8"/>

<img width="962" height="117" src="../_resources/33c1a7fa98eb45e9ae854dfb68bdd10e.png"/>

 <img width="962" height="507" src=":/c2d451178661488f9e93e8739125d9de"/>

<img width="962" height="487" src="../_resources/69eb8914302f468284ba88a6c5e2e3e3.png"/>

## <a id="t2"></a><a id="t2"></a>Echarts+Webpack:

![](:/d2a08646e43145ddaad86fd687e2d49a)

然后就可以使用webpack命令了：

<img width="962" height="639" src=":/c507ba9f5b96451a88e4d2ab8b11a92b"/>

现在报错是因为还没创建webpack配置文件：

![](4220f9246c624d27b6c18a4774e2caa8.png)

引入echarts：

![](483c68d19d7b449ab04f0988cd66470b.png)

然后

<img width="962" height="615" src=":/c5f6796b69b64abd8625b46d124ced55"/>

<img width="962" height="334" src="../_resources/572f3ad5bccc4419853ca016433a3c21.png"/> 直接引用模块化是错误的，需要使用webpack构建，引用构建后的源码：

<img width="962" height="650" src=":/9023ba29272047ba8a3cd93bda784765"/>

 然后修改引入:

<img width="962" height="423" src="../_resources/36963f7d925f42d88c5fe0939e7b4594.png"/>

常见Echarts图表：

可查看官网，相当丰富

* * *

### <a id="t3"></a><a id="t3"></a>实操：ECharts4实现数据报表组件（vue）

<img width="962" height="956" src=":/cfc5261c799248e9a3086b4b7955af14"/>

APP.vue:

```


1.  <template>
    
2.    <div id="app">
    
3.      <sales-report></sales-report>
    
4.    </div>
    
5.  </template>
    

7.  <script>
    
8.  import SalesReport from '@/components/SalesReport'
    
9.  export default {
    
10.   name: 'App',
    
11.   components: {
    
12.     SalesReport,
    
13.   }
    
14. }
    
15. </script>
    

17. <style>
    
18.   html, body {
    
19.     width: 100%;
    
20.     height: 100%;
    
21.     padding: 0;
    
22.     margin: 0;
    
23.     background: #eee;
    
24.   }
    
25.   #app {
    
26.     width: 100%;
    
27.     height: 100%;
    
28.     padding: 20px;
    
29.     box-sizing: border-box;
    
30.   }
    
31. </style>
    


```

index.vue:

```


1.  <template>
    
2.      <div class="language-report">
    
3.          <div class="header">编程语言hot榜</div>
    
4.          <div class="content">
    
5.              <div class="content-title-wrapper">月增长率</div>
    
6.              <div class="content-index-wrapper">
    
7.                  <span class="arrow-star">
    
8.                      <svg width="100" height="100" viewBox="0 0 190 180" 
    
9.                      fill="none" xmlns="http://www.w3.org/2000/svg">
    
10.                     <path d="M95 0L117.227 68.4073H189.155L130.964 110.685L153.191 179.093L95 136.815L36.8093 179.093L59.0361 110.685L0.845406 68.4073H72.7731L95 0Z" fill="#f1c40f"/>
    
11.                     </svg>
    
12.                 </span>
    
13.                 <span class="percentage">98<span>%</span></span>
    
14.                 <span class="text">+99,999</span>
    
15.             </div>
    
16.             <div id="content-chart"/>
    
17.             <div class="content-circle-wrapper">
    
18.                 <div 
    
19.                 :class="['circle', selectedIndex === index ? 'selected' : '']" 
    
20.                 @click="change(index)"
    
21.                 v-for="(item,index) in circle" 
    
22.                 :key="index"/>
    
23.             </div>
    
24.             <div class="content-bottom-wrapper">编程语言流行趋势</div>
    
25.         </div>
    
26.         <div class="footer">
    
27.             <div class="footer-wrapper">
    
28.                 <div class="left">
    
29.                     <div class="footer-title">Go使用人数</div>
    
30.                     <div class="footer-sub-title">5月累计人数</div>
    
31.                 </div>
    
32.                 <div class="right">
    
33.                     <small>人</small> 300,254,00
    
34.                 </div>
    
35.             </div>
    
36.             <div class="progress-wrapper">
    
37.                 <div class="progress-bg">
    
38.                     <div class="progress-current" :style="{width:`${progress*100}%`}"></div>
    
39.                 </div>
    
40.             </div>
    
41.             <div class="footer-text">
    
42.                 <div>人数增长率</div>
    
43.                 <div>89%</div>
    
44.             </div>
    
45.         </div>
    
46.     </div>
    
47. </template>
    
48. <script>
    
49. import ECharts from 'echarts';
    
50. export default {
    
51.     data() {
    
52.         return {
    
53.             circle: new Array(3),
    
54.             selectedIndex: 0,
    
55.             progress: 0.75,
    
56.         }
    
57.     },
    
58.     methods: {
    
59.         change(index) {
    
60.             this.selectedIndex = index;
    
61.             this.genChart();
    
62.         },
    
63.         genChart() {
    
64.             // 获取数据源
    
65.             const mockData = [];
    
66.             for (let i=0;i<10;i++){
    
67.                mockData.push(Math.floor( Math.random() * 100) + 200)
    
68.             }
    
69.             // 获取chart对应的dom
    
70.             const chartDom = document.getElementById('content-chart')
    
71.             // 初始化echarts对象
    
72.             const chart = ECharts.init(chartDom)
    
73.             // 渲染参数
    
74.             const options = {
    
75.                 xAxis: {
    
76.                     type: 'category',
    
77.                     show: false,
    
78.                 },
    
79.                 yAxis: {
    
80.                     show: false,
    
81.                     min: 0,
    
82.                     max: 400,
    
83.                 },
    
84.                 series: [{
    
85.                     data: mockData,
    
86.                     type: 'line',
    
87.                     smooth: true,
    
88.                     areaStyle: {
    
89.                         color: '#1abc9c'
    
90.                     },
    
91.                     lineStyle: {
    
92.                         width: 4,
    
93.                         color: '#2ecc71'
    
94.                     },
    
95.                     itemStyle: {
    
96.                         borderWidth: 8,
    
97.                         color: '#3498db'
    
98.                     }
    
99.                 }],
    
100.                grid: {
    
101.                    top: 0,
    
102.                    bottom: 0,
    
103.                    right: -30,
    
104.                    left: -30,
    
105.                },
    
106.                tooltip: {
    
107.                    trigger: 'axis',
    
108.                    axisPointer: {
    
109.                        type: 'cross',
    
110.                        label: {
    
111.                            backgroundColor: '#6a7985'
    
112.                        }
    
113.                    }
    
114.                }
    
115.            }
    
116.            // 渲染图表
    
117.            chart.setOption(options)
    
118.        },
    
119.    },
    
120.    mounted() {
    
121.            this.genChart();
    
122.            this.task = setInterval(() => {
    
123.                let index = this.selectedIndex
    
124.                index++
    
125.                if(index > this.circle.length){
    
126.                    index = 0;
    
127.                }
    
128.                this.change(index)//
    
129.            }, 3000);
    
130.    },
    
131.    destroyed() {
    
132.        if(this.task){
    
133.            clearInterval(this.task)
    
134.        }
    
135.    },
    
136.}
    
137.</script>
    
138.<style lang="scss">
    
139.    .language-report {
    
140.        display: flex;
    
141.        flex-direction: column;
    
142.        width: 100%;
    
143.        height: 100%;
    
144.        background: #fff;
    
145.        box-shadow: 0 2px 8px rgba(4,9,20,.3),0 2px 8px rgba(4,9,20,.3),0 2px 8px rgba(4,9,20,.3),0 2px 8px rgba(4,9,20,.3);
    
146.        .header {
    
147.            width: 100%;
    
148.            height: 50px;
    
149.            background: orange;
    
150.            border-bottom: 2px solid #eee;
    
151.            box-sizing: border-box;
    
152.            color: rgba(13,27,62,.7);
    
153.            padding-left: 20px;
    
154.            display: flex;
    
155.            align-items: center;
    
156.            font-weight: bold;
    
157.            color: #fff;
    
158.        }
    
159.        .content {
    
160.            flex: 1;
    
161.            width: 100%;
    
162.            padding: 0 28px;
    
163.            box-sizing: border-box;
    
164.            display: flex;
    
165.            flex-direction: column;
    
166.            .content-title-wrapper {
    
167.                padding-top: 28px;
    
168.                color: #1abc9c;
    
169.                font-size: 13px;
    
170.                font-weight: bold;
    
171.            }
    
172.            .content-index-wrapper {
    
173.                display: flex;
    
174.                align-items: center;
    
175.                .arrow-star {
    
176.                    svg {
    
177.                        width: 25px;
    
178.                    }
    
179.                }
    
180.                .percentage {
    
181.                    font-size: 35px;
    
182.                    font-weight: 700;
    
183.                    color: #333;
    
184.                    margin-left: 15px;
    
185.                    span {
    
186.                        font-size: 28px;
    
187.                        font-weight: 400;
    
188.                        color: #999;
    
189.                        margin-left: 2px;
    
190.                    }
    
191.                }
    
192.                .text {
    
193.                    margin-left: 15px;
    
194.                    color: #1abc9c;
    
195.                    font-weight:bold;
    
196.                }
    
197.            }
    
198.            #content-chart {
    
199.                flex: 1;
    
200.                width: 100%;
    
201.            }
    
202.            .content-circle-wrapper {
    
203.                display: flex;
    
204.                align-items: center;
    
205.                justify-content: center;
    
206.                margin-top: 20px;
    
207.                .circle {
    
208.                    width: 10px;
    
209.                    height: 10px;
    
210.                    background: #fff;
    
211.                    border: 3px solid #3498db;
    
212.                    border-radius: 50%;
    
213.                    margin: 0 5px;
    
214.                    &.selected {
    
215.                        background: #3498db;
    
216.                    }
    
217.                }
    
218.            }
    
219.            .content-bottom-wrapper {
    
220.                margin: 10px 0;
    
221.                color: #999;
    

223.            }
    

225.        }
    
226.        .footer {
    
227.            display: flex;
    
228.            flex-direction: column;
    
229.            width: 100%;
    
230.            height: 120px;
    
231.            border-top: 2px solid #eee;
    
232.            box-sizing: border-box;
    
233.            .footer-wrapper{
    
234.                display: flex;
    
235.                padding: 14px 14px 0;
    
236.                .left {
    
237.                    .footer-title {
    
238.                        font-size: 13px;
    
239.                        font-weight: 700;
    
240.                        color: #333;
    
241.                    }
    
242.                    .footer-sub-title {
    
243.                        font-size: 13px;
    
244.                        font-weight: 400;
    
245.                        color: #999;
    
246.                    }
    
247.                }
    
248.                .right {
    
249.                    flex: 1;
    
250.                    text-align: right;
    
251.                    color: #1abc9c;
    
252.                    font-size: 25px;
    
253.                    font-weight: 700;
    
254.                    small {
    
255.                        font-weight: 400;
    
256.                    }
    
257.                }
    
258.            }
    
259.            .progress-wrapper {
    
260.                flex: 1;
    
261.                display: flex;
    
262.                justify-content: center;
    
263.                align-items: center;
    
264.                padding: 14px;
    
265.                .progress-bg {
    
266.                    position: relative;
    
267.                    width: 100%;
    
268.                    height: 7px;
    
269.                    background: #95a5a6;
    
270.                    border-radius: 3px;
    
271.                    .progress-current {
    
272.                        position: absolute;
    
273.                        left: 0;
    
274.                        top: 0;
    
275.                        height: 7px;
    
276.                        background: #3498db;
    
277.                        border-radius: 3.5px;
    
278.                        &::after {
    
279.                            content: '';
    
280.                            position: absolute;
    
281.                            top: 0;
    
282.                            left: 0;
    
283.                            right: 0;
    
284.                            bottom: 0;
    
285.                            width: 100%;
    
286.                            background: #fff;
    
287.                            opacity: 0;
    
288.                            animation: progress-active 2s ease infinite
    
289.                        }
    
290.                        @keyframes progress-active {
    
291.                            from {
    
292.                                width: 0;
    
293.                                opacity: 0;
    
294.                            }
    
295.                            to {
    
296.                                width: 100%;
    
297.                                opacity: .3;
    
298.                            }
    
299.                        }
    
300.                    }
    
301.                }
    
302.            }
    
303.            .footer-text {
    
304.                display: flex;
    
305.                justify-content: space-between;
    
306.                padding: 0 14px 14px;
    
307.                font-size: 13px;
    
308.                color: #999;
    
309.            }
    
310.        }
    
311.    }
    
312.</style>
    


```

显示推荐内容

-  <img width="22" height="22" src=":/af05f9b0219a4019883d2ffcca27787f"/> <a id="is-like-span"></a>点赞
- [<img width="22" height="22" src=":/a1fe339e45ea4f1abbf761d8056cfd5e"/>评论](#commentBox)
- <img width="22" height="22" src=":/7eb682917d7145c192abf868c49bfc31"/>分享
- <img width="22" height="22" src=":/8a858596d94f4494b08d50ffd69cb024"/><a id="is-collection"></a>收藏 <a id="get-collection"></a>2 
- <a id="toolreward"></a><img width="22" height="22" src="../_resources/5b1a24ad3d0f477a862235c7a76aebff.png"/>打赏
- <img width="22" height="22" src="../_resources/37a4e5a8e5a24b46b99837160b3915f6.png"/>举报
- 订阅博主
- 关注
- 一键三连
    

<a id="commentBox"></a>

<a id="commentsedit"></a>

[<img width="30" height="30" src=":/f42d2202145241cf93c08cdb4aa2c0c0"/>](https://blog.csdn.net/qq_43528695)

<img width="18" height="18" src=":/97471fd87ff34fcda0e08f65a621f23f"/>

<img width="24" height="24" src=":/7fd7180821e4456b9e74dcd9874c592a"/><img width="24" height="24" src="../_resources/60f51a84a4954a6e81a45bc350cca971.png"/>举报