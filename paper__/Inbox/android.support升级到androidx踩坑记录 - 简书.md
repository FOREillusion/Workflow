android.support升级到androidx踩坑记录 - 简书

[](https://www.jianshu.com/)

[登录](https://www.jianshu.com/sign_in)[注册](https://www.jianshu.com/sign_up)[写文章](https://www.jianshu.com/writer)

[首页](https://www.jianshu.com/)[下载APP](https://www.jianshu.com/apps?utm_medium=desktop&utm_source=navbar-apps)

# android.support升级到androidx踩坑记录

[![](https://upload.jianshu.io/users/upload_avatars/2423227/e6879bb9-43e5-4345-a0ae-c38ae3417aa2.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/80/h/80)程序员张晴天](https://www.jianshu.com/u/3d33e7bc60b3)

# android.support升级到androidx踩坑记录

[![](https://upload.jianshu.io/users/upload_avatars/2423227/e6879bb9-43e5-4345-a0ae-c38ae3417aa2.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96)](https://www.jianshu.com/u/3d33e7bc60b3)

[程序员张晴天](https://www.jianshu.com/u/3d33e7bc60b3)

62019.02.13 20:49:08字数 1,081阅读 65,414

年前想着Google老大之前提醒过将项目升级到androidx，所以年前一通操作猛如虎把Android Studio唰唰升级到3.2.1，自然也把gradle升级到gradle-4.10.1-all。

没想到这只是开始作死的第一步，gradle升级的痛相信升级过的人都懂。

升级完项目后，不出所料的Android Studio呈现满江红的样式，十分酸爽。在我一次次修改，不断clean又build项目，天真的以为马上可以编译通过在运行成功时，现实总是冷冷的抽打我的脸，依旧有报错，R文件都无法生成，谈何运行。

改了3天，才差不多将各种报错问题修改完成，项目成功运行。

下面记录一下修改的内容，也方便以后查阅。

升级Android Studio后，在 Refactor ---> Migrate to AndroidX 路径下可以将整个项目升级到AndroidX。

![](https://upload-images.jianshu.io/upload_images/2423227-a9c83d27ef838162.png?imageMogr2/auto-orient/strip|imageView2/2/w/283)

Migrate to AndroidX.png

所以接下来的修改也是在自动升级后的手动修改。

文末参考文章是Google官方的说明，可以查看里面更详细的指引和说明[Migrating to AndroidX](https://developer.android.com/jetpack/androidx/migrate)。

#### 本文目录：

1.手动配置gradle.properties
2.app/build.gradle修改
3.java文件修改
4.布局文件xml修改
5.部分view不可用修改
6.其他问题
7.gradle命令帮助查找问题
8.参考文章

# 1.手动配置gradle.properties

```bash
android.enableJetifier=true
android.useAndroidX=true 
```

# 2.app/build.gradle修改

| 修改前android.support | 修改后androidx |
| --- | --- |
| compile | implementation |
| testCompile | testImplementation |
| debugCompile | debugImplementation |
| releaseCompile | releaseImplementation |

当然，这部分主要是因为gradle升级后提示需要的更新。
没写全，只写了主要的，Android Studio有提示，根据提示更新就行。

```cpp
android{
        //省略。。。。
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
} 
```

# 3.java文件修改

这里主要指import的修改，Android Studio自动帮助项目升级成androidx时，很多控件的路径在androidx中都变更了，所以需要全局搜索一键替换。

全局替换: Edit ->Find -> Replace in path

列举了一些常用的：

| 修改前android.support | 修改后androidx |
| --- | --- |
| import androidx.appcompat.widget.RecyclerView; | import androidx.recyclerview.widget.RecyclerView; |
| import androidx.core.view.ViewPager; | import androidx.viewpager.widget.ViewPager; |
| import androidx.core.view.PagerAdapter; | import androidx.viewpager.widget.PagerAdapter; |
| import androidx.core.app.Fragment; | import androidx.fragment.app.Fragment; |
| import androidx.core.app.FragmentManager; | import androidx.fragment.app.FragmentManager; |
| import androidx.core.app.FragmentTransaction; | import androidx.fragment.app.FragmentTransaction; |
| import androidx.core.content.LocalBroadcastManager; | import androidx.localbroadcastmanager.content.LocalBroadcastManager; |
| import androidx.appcompat.widget.DefaultItemAnimator; | import androidx.recyclerview.widget.DefaultItemAnimator; |
| import androidx.appcompat.widget.LinearLayoutManager; | import androidx.recyclerview.widget.LinearLayoutManager; |
| import androidx.appcompat.widget.GridLayoutManager; | import androidx.recyclerview.widget.GridLayoutManager; |
| import androidx.appcompat.widget.StaggeredGridLayoutManager; | import androidx.recyclerview.widget.StaggeredGridLayoutManager; |
| import android.support.design.widget.CoordinatorLayout; | import androidx.coordinatorlayout.widget.CoordinatorLayout; |
| import android.support.design.widget.CoordinatorLayout; | import androidx.coordinatorlayout.widget.CoordinatorLayout; |
| import android.support.design.widget.TabLayout; | import com.google.android.material.tabs.TabLayout; |
| import android.support.design.widget.AppBarLayout; | import com.google.android.material.appbar.AppBarLayout |

当整个项目因为未生成R文件满江红的时候，全局搜索替换这些常用控件会比较方便。

# 4.布局文件xml修改

在**3.java文件修改**中罗列了一些控件，同样也是全局搜索把这些对应的控件修改成androidx的就好。

列举几个：

| 修改前android.support | 修改后androidx |
| --- | --- |
| &lt;android.support.v4.widget.NestedScrollView/&gt; | &lt;androidx.core.widget.NestedScrollView/&gt; |
| &lt;android.support.v4.widget.Space/&gt; | &lt;Space/&gt; |
| &lt;android.support.v7.widget.AppCompatTextView/&gt; | &lt;TextView/&gt; |
| &lt;androidx.appcompat.widget.CardView/&gt; | &lt;androidx.cardview.widget.CardView/&gt; |

**注意！！！**

不管是java还是xml也好，在Android Studio全局搜索的情况下还是有可能搜漏，所以可能还是需要挨个文件查看替换。

# 5.部分view不可用修改

之前手动拷贝了support包中的TabItem（TabLayout需要使用的一个类）出来使用，在升级到androidx后发现无法使用了。

support中的代码：

```java
 public TabItem(Context context, AttributeSet attrs) {
        super(context, attrs);

        final TintTypedArray a = TintTypedArray.obtainStyledAttributes(context, attrs,
               android.support.design.R.styleable.TabItem);
        mText = a.getText(android.support.design.R.styleable.TabItem_android_text);
        mIcon = a.getDrawable(android.support.design.R.styleable.TabItem_android_icon);
        mCustomLayout = a.getResourceId(android.support.design.R.styleable.TabItem_android_layout, 0);
        a.recycle();
    } 
```

a.getText()、a.getDrawable()、a.getResourceId()都报错，提示TintTypedArray.getText()或TintTypedArray.getDrawable()或TintTypedArray.getResourceId()只能在同一个library group(groupId=androidx.appcompat)中被调用，

从TintTypedArray的源码可知，是在源码中做了限制的原因：

```kotlin
@RestrictTo(LIBRARY_GROUP)
public class TintTypedArray {} 
```

# 6.其他问题

app module所依赖的一些library我也升级到了androidx，在将library中的build.gradle里的compile修改成implementation的时候注意，如果这里依赖的对象需要全局使用的话，就要使用**api**关键字了。

比如：

```php
修改前：
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
}

修改后：
dependencies {
    api fileTree(dir: 'libs', include: ['*.jar'])
} 
```

# 7.gradle命令帮助查找问题

在我一次又一次编译项目时，始终找不到是java文件或者xml文件哪里出错了，logcat、message也不报错，这种时候是最愁的。

直到我找到了大杀器：gradle命令

在terminal本项目路径下使用下面这条命令来编译项目：

> ./gradlew compileDebugJavaWithJavac

编译不通过就会报错，报错信息里有报错的文件以及错误的位置，这条命令帮了大忙，可以说是相当感动了，双击回车就是666。

以上就基本上写完了，如果本文有错误或不妥之处欢迎大家指正和讨论，谢谢。

#### 参考文章

- [Migrating to AndroidX](https://developer.android.com/jetpack/androidx/migrate)

如果对你有帮助的话，点赞、评论、赞赏都是对我的鼓励，也是支持我写下去的动力，谢谢！

72人点赞

[Android学习](https://www.jianshu.com/nb/30348106)

"来，进来登记一下。"

还没有人赞赏，支持一下

[![  ](https://upload.jianshu.io/users/upload_avatars/2423227/e6879bb9-43e5-4345-a0ae-c38ae3417aa2.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/100/h/100)](https://www.jianshu.com/u/3d33e7bc60b3)

[程序员张晴天](https://www.jianshu.com/u/3d33e7bc60b3 "程序员张晴天")

总资产32 (约2.94元)共写了2.0W字获得287个赞共58个粉丝

### 被以下专题收入，发现更多相似内容

[![](https://upload.jianshu.io/collections/images/1787065/android.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)android](https://www.jianshu.com/c/bbfe95471419)[![](https://upload.jianshu.io/collections/images/515831/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7_2017-04-13_22.29.58.png?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)Android开发](https://www.jianshu.com/c/e0aed84e49c6)[![](https://upload.jianshu.io/collections/images/1786632/ic_launcher.png?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)Google移...](https://www.jianshu.com/c/0e16e0a060d8)[![](https://upload.jianshu.io/collections/images/498058/android.graphics.Bitmap_dccef17.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)Android技术](https://www.jianshu.com/c/c886ee25bd34)[![](https://upload.jianshu.io/collections/images/305132/android.graphics.Bitmap_d5db3de.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)Android 知识](https://www.jianshu.com/c/ea06bc795515)

### 推荐阅读[更多精彩内容](https://www.jianshu.com/)

- [ButterKnife VS ViewBinding，谁才是findView的未来？](https://www.jianshu.com/p/1315fdba8768)
    
    最近Android Studio更新到了4.1版本，发现项目中使用ButterKnife注解id的代码出现了警告，...
    
    [![](https://upload.jianshu.io/users/upload_avatars/24388310/154a2dd8-de3a-441c-829e-d4b4d494b3e1.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)码农的书柜](https://www.jianshu.com/u/d4bd711c721e)阅读 1,370评论 7赞 22
    
    [![](https://upload-images.jianshu.io/upload_images/24388310-49348fa31bd40959.png?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/1315fdba8768)
- [Android之RecyclerView的StaggeredGridLayoutManager使用](https://www.jianshu.com/p/f1999c3f6a26)
    
    StaggeredGridLayoutManager StaggeredGridLayoutManager（瀑布流...
    
    [![](https://upload.jianshu.io/users/upload_avatars/11345863/e0bb5150-b8a1-4e7c-8c66-878957522fb4?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)Lee_5566](https://www.jianshu.com/u/3a1e20d421df)阅读 759评论 0赞 2
    
    [![](https://upload-images.jianshu.io/upload_images/11345863-caa90aba71298531.png?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/f1999c3f6a26)
- [android10系统手机报错：does not meet the requirements ...](https://www.jianshu.com/p/6036f688666d)
    
    今天安卓打包的apk在android10系统的设备上安装后刚点应用就闪退，通过Android studio连接设备...
    
    [![](https://upload.jianshu.io/users/upload_avatars/11880780/549558cf-716b-43c2-b465-7fe24473fd18.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)指尖劃過諾言](https://www.jianshu.com/u/64d5cd2bc0f9)阅读 179评论 0赞 0
    
- [GridLayoutManager怎么居中?](https://www.jianshu.com/p/3e0749a1cf0c)
    
    默认效果是这样 期望的效果是这样 期望是每一行类似ConstraintLayout中的chain spread i...
    
    [![](https://upload.jianshu.io/users/upload_avatars/3290652/3981bf8d3c78.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)pokercc](https://www.jianshu.com/u/5be17555cdd4)阅读 317评论 0赞 1
    
    [![](https://upload-images.jianshu.io/upload_images/3290652-4f47fab7a37d93f2.png?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/3e0749a1cf0c)
- [在Windows上使用Nuitka将Python文件打包成exe文件](https://www.jianshu.com/p/9d55350bfe32)
    
    Nuitka是用于将Python文件打包成可执行文件的工具，类似于PyInstaller，但是相较而言Nuitka...
    
    [![](https://upload.jianshu.io/users/upload_avatars/3959253/76f92758-8f57-48f6-85ac-fa4a5a10bb2d.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/48/h/48)geekpy](https://www.jianshu.com/u/e5fa627c0613)阅读 1,325评论 0赞 15
    
    [![](https://upload-images.jianshu.io/upload_images/3959253-e609d3d1fda3f8c3.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240)](https://www.jianshu.com/p/9d55350bfe32)

[![](https://upload.jianshu.io/users/upload_avatars/2423227/e6879bb9-43e5-4345-a0ae-c38ae3417aa2.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/90/h/90)](https://www.jianshu.com/u/3d33e7bc60b3)

[程序员张晴天](https://www.jianshu.com/u/3d33e7bc60b3)

总资产32 (约2.94元)

[JS获取屏幕宽高属性总结](https://www.jianshu.com/p/450ec7db60a2)

阅读 158

[web高仿樊登H5/微信音乐播放悬浮球效果](https://www.jianshu.com/p/f2aeccdea346)

阅读 198

评论12

赞72

72赞73赞

赞赏

下载App