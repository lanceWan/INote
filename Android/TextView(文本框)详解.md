# Android TextView(文本框)详解

> 1A视频比较简单，没有讲太多的知识，下面的笔记结合自己在网上找的相关资料点进行补充，来扩展一下TextView相关的知识

## 1.1Android相关介绍

 1. Android中 `view` 视图相关的概念视频中讲解已经很生动了，这里就不多废话了
 2. Android中命名规范为驼峰命名法
 3. Android中的注释和大部分语言注释一致，单行注释可以用`//`，多行注释用`/* 内容 */`
 4. `dp`和`sp`相关概念百度一下就知道了

## 1.2基本属性详解
![TextView基本属性][1]

布局代码：
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:gravity="center"
    tools:context=".MainActivity">

    <TextView
        android:text="Hello World!"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:gravity="center"
        android:textColor="@android:color/darker_gray"
        android:textStyle="bold|italic"
        android:background="#000000"
        android:textSize="18sp"/>
</RelativeLayout>
```

上面TextView用到的属性如下

 - layout_width：组件的宽度，一般写：**wrap_content**或者**match_parent(fill_parent)**，前者是控件显示的内容多大，控件就多大，而后者会填满该控件所在的父容器；当然也可以设置成特定的大小，比如我这里为了显示效果，设置成了200dp。
 - layout_height：组件的宽度，参数同上
 - gravity：设置控件中内容的对齐方向，TextView中是文字，ImageView中是图片等等。
 - text：设置显示的文本内容，一般我们是把字符串写到string.xml文件中，然后通过@String/xxx取得对应的字符串内容的，这里为了方便我直接就写到""里，不建议这样写！！！考虑到现在是1A的笔记，所以就先这样写
 - textColor：设置字体颜色，调用的是android系统中自带的颜色，也可以用16进制来表示颜色。ky通过colors.xml资源来引用，不建议别直接这样写！理由同上
 - textStyle：设置字体风格，三个可选值：**normal**(无效果)，**bold**(加粗)，**italic**(斜体)
 - textSize：字体大小，单位一般是用sp！
 - background：控件的背景颜色，跟HTML中的功能相同，可以是图片哦！


----------
## 2.扩展知识
### 2.1带有阴影的TextView
![带阴影的TextView][2]

实现代码:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:shadowColor="#9c27b0"
        android:shadowDx="10.0"
        android:shadowDy="8.0"
        android:shadowRadius="3.0"
        android:text="带阴影的TextView"
        android:textColor="#5677fc"
        android:textSize="30sp"/>

</LinearLayout>
```
上面代码中用到的TextView属性:

 - android:shadowColor:设置阴影颜色,需要与shadowRadius一起使用
 - android:shadowRadius:设置阴影的模糊程度,设为0.1就变成字体颜色了
 - android:shadowDx:设置阴影在水平方向的偏移,就是水平方向阴影开始的横坐标位置
 - android:shadowDy:设置阴影在竖直方向的偏移,就是竖直方向阴影开始的纵坐标位置

### 2.2带图片的TextView
在很多app中，我们经常会看到这样的菜单:

![QQ截图][3]

如图，要实现上面的想法，看过1A的小伙伴的想法是：一个ImageView用于显示图片+一个TextView用于显示文字，把他们丢到一个LinearLayout中，接着依次创建三个这样的小布局，再另外放到一个大的LinearLayout中，效果就可以实现了,但会不会有点繁琐?其实TextView就可以实现这个功能。

**基本用法**

设置图片的核心其实就是:drawableTop(上)，drawableButtom(下)，drawableLeft(左)，drawableRight(右)来设置图片位于文字的四个方向。另外，你也可以使用drawablePadding来设置图片与文字间的间距！

效果图：

![带图片TextView][4]

实现代码：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="晚黎笔记"
        android:drawableLeft="@drawable/social_facebook"
        android:drawableTop="@drawable/social_googleplus"
        android:drawableRight="@drawable/social_twitter"
        android:drawableBottom="@drawable/social_rss"
        android:drawablePadding="10dp"/>

</LinearLayout>
```

### 2.3实现跑马灯效果的TextView
简单说下什么是跑马灯，就是生活中很多广告LED屏幕，有一行字一直循环滚滚动这样，好吧还是看看 实现效果图，一看就懂的了~

![TextView实现跑马灯效果][5]

代码实现：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center">
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:singleLine="true"
        android:ellipsize="marquee"
        android:marqueeRepeatLimit="marquee_forever"
        android:focusable="true"
        android:focusableInTouchMode="true"
        android:textColor="#ff006a"
        android:text="StudyJams，给自己一次变强的机会！一切会好起来，明天又是新的一天！"
        />

</LinearLayout>
```

## 3小结
对于TextView的属性进行了额外的扩展，当然这只是其中的一部分，相信会给你以后实际开发中带来一点便利。本人很少做笔记，能力有限，写出来的东西可能有纰漏的地方，欢迎大家指出~

对于TextView的其他属性，大家可以查一下API，我搜到了一个网址，不用翻墙也可以看Android的API，速度也不错，这里贡献给大家：[http://androiddoc.qiniudn.com/index.html][6]

对应的TextView API地址：[http://androiddoc.qiniudn.com/reference/android/widget/TextView.html][7]

  [1]: http://7xsgy7.com1.z0.glb.clouddn.com/image/android/Android-20160331143016.png
  [2]: http://7xsgy7.com1.z0.glb.clouddn.com/image/android/Android-20160331144013.png
  [3]: http://7xsgy7.com1.z0.glb.clouddn.com/image/android/Android-20160331145354.png
  [4]: http://7xsgy7.com1.z0.glb.clouddn.com/image/android/Android-20160331161945.png
  [5]: http://7xsgy7.com1.z0.glb.clouddn.com/image/android/text.gif
  [6]: http://androiddoc.qiniudn.com/index.html
  [7]: http://androiddoc.qiniudn.com/reference/android/widget/TextView.html