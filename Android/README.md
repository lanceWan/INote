# 晚黎的个人笔记
andorid笔记

#ImageView(图像视图)
## 引言：
此篇文章介绍的基础控件是：ImageView(图像视图)，一看便知是用来显示图像的一个View或者说控件！官方API地址：[ImageView](http://androiddoc.qiniudn.com/reference/android/widget/ImageView.html)，本章介绍ImageView以下内容：

1. ImageView的src属性和background属性的区别
2. adjustViewBounds设置图像缩放时是否按长宽比
3. scaleType设置缩放类型
4. (视野)圆形的ImageView

##src属性和background属性的区别
>在API文档中可以发现ImageView有两个可以设置图片的属性，分别是`src`和`background`属性

**区别**:

* background通常指的是**背景**，src指的**内容**
* 使用src添加的图片时是按照图片的大小**直接填充**并不会进行**拉伸**
* 使用background添加的图片时，则会根据ImageView给定的宽度来进行**拉伸**

下面我们直接用代码来认识一下两个属性的区别：
###代码区别验证
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!--background图片本身宽高-->
    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/avatar"/>

    <!--background固定宽度-->
    <ImageView
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:background="@drawable/avatar"/>

    <!--src属性-->
    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/avatar"/>

    <!--src固定宽度-->
    <ImageView
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:src="@drawable/avatar"/>
</LinearLayout>
```


