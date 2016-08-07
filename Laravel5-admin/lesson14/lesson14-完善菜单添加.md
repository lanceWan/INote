# Laravel5.2实战视频之完善添加菜单
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

# 改进
> 根据群友的建议，将视频改进了一下。欢迎大家提供宝贵的建议~

1. 缩短视频时间，避免啰嗦
2. 以知识点来讲解

#完善添加菜单
**Presenter模式**

若将逻辑判断都写在 `View` ，会造成 `View` 肥大而难以维护，我们将使用 `Presenter` 模式辅助 `View` ，将相关的提示逻辑判断封装到不同的 `Presenter` ，方便以后维护。

```
@inject('menu','App\Repositories\Presenter\MenuPresenter')
```


# 项目资料
1. [后台主题](https://github.com/puikinsh/gentelella)
2. [datatables](http://datatables.club/)
3. [layer](http://layer.layui.com/)
4. [Laravel学院](http://laravelacademy.org/)
5. [http://www.golaravel.com/](http://www.golaravel.com/)
6. [https://packagist.org/](https://packagist.org/)
7. [Laravel多语言](https://github.com/caouecs/Laravel-lang)
8. [Redis](https://github.com/MSOpenTech/redis)
9. [nestable](https://github.com/dbushell/Nestable)
10. [flash](https://packagist.org/packages/laracasts/flash)

# 教程资料
1. 项目源码：[laravel-tutorial](https://github.com/lanceWan/laravel-tutorial)
2. 教学笔记：[https://github.com/lanceWan/INote/tree/master/Laravel5-admin](https://github.com/lanceWan/INote/tree/master/Laravel5-admin)