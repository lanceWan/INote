# Android TextView(文本框)详解

> 1A视频比较简单，没有讲太多的知识，下面的笔记结合自己在网上找的相关资料点进行补充，来扩展一下TextView相关的知识

## 1.1Android相关介绍

 1. Android中 `view` 视图相关的概念视频中讲解已经很生动了，这里就不多废话了
 2. Android中命名规范为驼峰命名法(视频翻译为骆驼...虽然跟骆驼有点关联，准确来说是驼峰命名法)
 3. Android中的注释和大部分语言注释一致，单行注释可以用`//`，多行注释用`/* 内容 */`
 4. `dp`和`sp`相关概念百度一下就知道了，粘贴

## 1.2基本属性详解

![简单的TextView界面][1]


  
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
效果图：
![enter description here][2]


  [1]: ./images/1459342108637.jpg "简单的TextView界面"
  [2]: ./images/1459343856147.jpg "1459343856147.jpg"

