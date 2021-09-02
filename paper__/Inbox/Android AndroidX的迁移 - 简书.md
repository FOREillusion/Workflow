Android AndroidX的迁移 - 简书

[](https://www.jianshu.com/)

[登录](https://www.jianshu.com/sign_in)[注册](https://www.jianshu.com/sign_up)[写文章](https://www.jianshu.com/writer)

[首页](https://www.jianshu.com/)[下载APP](https://www.jianshu.com/apps?utm_medium=desktop&utm_source=navbar-apps)

# Android AndroidX的迁移

[翻译不了的声响](https://www.jianshu.com/u/92c4b917f8f3)

# Android AndroidX的迁移

Google 2018 IO 大会推出了 Android新的扩展库 AndroidX，用于替换原来的 Android扩展库，将原来的`android.*`替换成`androidx.*`；只有包名和Maven工件名受到影响，原来的类名，方法名和字段名不会更改。接下来我们来看看使用 AndroidX的扩展库需要哪些配置。

##### 1\. AndroidX变化

**1）常用依赖库对比：**

| Old build artifact | AndroidX build artifact |
| --- | --- |
| `com.android.support:appcompat-v7:28.0.2` | `androidx.appcompat:appcompat:1.0.0` |
| `com.android.support:design:28.0.2` | `com.google.android.material:material:1.0.0` |
| `com.android.support:support-v4:28.0.2` | `androidx.legacy:legacy-support-v4:1.0.0` |
| `com.android.support:recyclerview-v7:28.0.2` | `androidx.recyclerview:recyclerview:1.0.0` |
| `com.android.support.constraint:constraint-layout:1.1.2` | `androidx.constraintlayout:constraintlayout:1.1.2` |

 更多详细变化内容，可以[下载CSV格式](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Ftopic%2Flibraries%2Fsupport-library%2Fdownloads%2Fandroidx-artifact-mapping.csv)映射文件；

**2）常用支持库类对比：**

| Support Library class | AndroidX class |
| --- | --- |
| `android.support.v4.app.Fragment` | `androidx.fragment.app.Fragment` |
| `android.support.v4.app.FragmentActivity` | `androidx.fragment.app.FragmentActivity` |
| `android.support.v7.app.AppCompatActivity` | `androidx.appcompat.app.AppCompatActivity` |
| `android.support.v7.app.ActionBar` | `androidx.appcompat.app.ActionBar` |
| `android.support.v7.widget.RecyclerView` | `androidx.recyclerview.widget.RecyclerView` |

 更多详细变化内容，可以[下载CSV格式](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Ftopic%2Flibraries%2Fsupport-library%2Fdownloads%2Fandroidx-class-mapping.csv)映射文件。

##### 2\. AndroidX配置

**1）更新升级插件**

- 将AS更新至 **AS 3.2**及以上；
- Gradle 插件版本改为 **4.6**及以上；
    项目下 `gradle/wrapper/gradle-wrapper.propertie` 文件中的`distributionUrl`改为：

```
distributionUrl=https\://services.gradle.org/distributions/gradle-4.6-all.zip 
```

- compileSdkVersion 版本升级到 **28**及以上；
- buildToolsVersion 版本改为 **28.0.2**及以上。

插件更新提示

**2）开启迁移AndroidX**
 在项目的`gradle.properties`文件里添加如下配置：

```
android.useAndroidX=true
android.enableJetifier=true 
```

 表示项目启用 AndroidX 并迁移到 AndroidX。

**3）替换依赖库**
 修改项目app目录下的`build.gradle`依赖库：

```
implementation 'com.android.support:appcompat-v7:28.0.2' → implementation 'androidx.appcompat:appcompat:1.0.0'
implementation 'com.android.support:design:28.0.2'  → implementation 'com.google.android.material:material:1.0.0'
implementation 'com.android.support.constraint:constraint-layout:1.1.2' → implementation 'androidx.constraintlayout:constraintlayout:1.1.2'
... 
```

**4）修改支持库类**
 将原来`import`的`android.**`包删除，重新`import`新的`androidx.**`包；

```
import android.support.v7.app.AppCompatActivity; → import androidx.appcompat.app.AppCompatActivity; 
```

**5）一键迁移AndroidX库**
 AS 3.2 及以上版本提供了更加方便快捷的方法一键迁移到 AndroidX。选择菜单上的**ReFactor —— Migrate to AndroidX...** 即可。（如果迁移失败，就需要重复上面1，2，3，4步手动去修改迁移）

AndroidX 迁移

**注意：**如果你的项目compileSdkVersion 低于28，点击Refactor to AndroidX...会提示：

```
You need to have at least have compileSdk 28 set in your module build.gradle to refactor to androidx 
```

提示让你使用不低于28的sdk，升级最新到SDK，然后点击 **Migrate to AndroidX...**，AS就会自动将项目重构并使用AndroidX库。

##### 3\. AndroidX迁移问题

[《Android Support库和AndroidX冲突问题》](https://www.jianshu.com/p/7507c25fd986)

##### 4\. AndroidX影响

  虽然说目前对我们没有多大影响，我们可以不使用仍然使用旧版本的支持库，毕竟没有强制，但长远来看还是有好处的。AndroidX重新设计了包结构，旨在鼓励库的小型化，支持库和架构组件包的名字也都简化了；而且也是减轻Android生态系统碎片化的有效方式。

##### 参考

[AndroidX 官方文档，开启传送门](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.android.google.cn%2Fjetpack%2Fandroidx%2Fmigrate)

禁止转载，如需转载请通过简信或评论联系作者。

### 推荐阅读[更多精彩内容](https://www.jianshu.com/)

- [史上最优美的Android原生UI框架XUI使用指南](https://www.jianshu.com/p/8eefa9386773)
    
    项目简介 XUI\[https://github.com/xuexiangjys/XUI\]是一个简洁而又优雅的And...
    
    [xuexiangjys](https://www.jianshu.com/u/6bf605575337)阅读 3,180评论 13赞 37
    
    [](https://www.jianshu.com/p/8eefa9386773)
- [Spring Boot 快速迁移至 Quarkus](https://www.jianshu.com/p/6910a99c09e8)
    
    Quarkus 是一个目前非常火的 Java 应用开发框架，定位是轻量级的微服务框架。，Quarkus 提供了优秀...
    
    [冷冷zz](https://www.jianshu.com/u/43565957dba2)阅读 2,122评论 1赞 22
    
- [Spring Boot 2.4.0 正式发布！全新的配置处理机制，拥抱云原生！](https://www.jianshu.com/p/b7f29d2b07a0)
    
    2020年11月12日，Spring官方发布了Spring Boot 2.4.0 GA的公告。 在这个版本中增加了...
    
    [程序猿DD](https://www.jianshu.com/u/6a622d516e32)阅读 1,589评论 0赞 17
    
- [Android之RecyclerView的StaggeredGridLayoutManager使用](https://www.jianshu.com/p/f1999c3f6a26)
    
    StaggeredGridLayoutManager StaggeredGridLayoutManager（瀑布流...
    
    [Lee_5566](https://www.jianshu.com/u/3a1e20d421df)阅读 790评论 0赞 2
    
    [](https://www.jianshu.com/p/f1999c3f6a26)
- [Kotlin协程学习3之协程与ViewModel通过viewModelScope构建网络请求基...](https://www.jianshu.com/p/b0af44996e09)
    
    完整完善工程代码见: https://github.com/WeDox/AndroidXDemo3\[https:/...
    
    [jdallen](https://www.jianshu.com/u/24ca40166757)阅读 714评论 1赞 1
    

评论26

赞161

161赞162赞

赞赏

下载App