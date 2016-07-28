# Laravel5.2实战视频之菜单表和路由定义
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

# entrust 方法报错问题

# 菜单表的建立
```
Schema::create('menus', function (Blueprint $table) {
    $table->increments('id');
    $table->string('name')->default('')->comment('菜单名称');
    $table->string('icon')->default('')->comment('菜单图标');
    $table->string('parent_id')->default('')->comment('父级菜单ID');
    $table->string('url')->default('')->comment('菜单链接');
    $table->string('heightlight_url')->default('')->comment('菜单高亮');
    $table->tinyInteger('sort')->unsigned()->default(0)->comment('排序');
    $table->timestamps();
});
```
# 路由配置
```
Route::group(['namespace' => 'Admin','prefix' => 'admin' ,'middleware' => ['auth']],function($router){
	// 首页路由
	require(__DIR__.'/Routes/HomeRoute.php');
});
```

```
<?php
$router->get('/','HomeController@index');
$router->resource('home','HomeController');
```

# 左右值无限分类 预排序遍历树算法资料
[http://blog.csdn.net/yaerfeng/article/details/8517479](http://blog.csdn.net/yaerfeng/article/details/8517479)
[http://blog.csdn.net/black_ox/article/details/26731015](http://blog.csdn.net/black_ox/article/details/26731015)
[http://blog.csdn.net/i_bruce/article/details/41558063](http://blog.csdn.net/i_bruce/article/details/41558063)

# 项目资料
1. [后台主题](https://github.com/puikinsh/gentelella)
2. [datatables](http://datatables.club/)
3. [layer](http://layer.layui.com/)
4. [Laravel学院](http://laravelacademy.org/)
5. [http://www.golaravel.com/](http://www.golaravel.com/)
6. [https://packagist.org/](https://packagist.org/)
7. [Laravel多语言](https://github.com/caouecs/Laravel-lang)
8. [Redis](https://github.com/MSOpenTech/redis)

# 教程资料
1. 项目源码：[laravel-tutorial](https://github.com/lanceWan/laravel-tutorial)
2. 教学笔记：[https://github.com/lanceWan/INote/tree/master/Laravel5-admin](https://github.com/lanceWan/INote/tree/master/Laravel5-admin)