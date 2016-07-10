# Laravel5.2实战视频之验证码包的使用
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

## 扩展包的查找
[https://packagist.org/](https://packagist.org/)

扩展包的推荐：
[疯狂Laravel](https://github.com/chiraggude/awesome-laravel)

[中文翻译版](http://laravelacademy.org/post/153.html)

## 使用方法
查看包安装说明

## 在项目中使用

1. composer安装 `composer require mews/captcha`
2. 注册provider `Mews\Captcha\CaptchaServiceProvider::class,`
3. 注册facade `'Captcha' => Mews\Captcha\Facades\Captcha::class,`
3. 生成配置文件 `php artisan vendor:publish`
4. 页面中使用
5. 点击图片刷新 `<img style="cursor: pointer;" src="{{captcha_src()}}" onclick="this.src='{{captcha_src()}}' + Math.random()">`
6. 重写AuthController 登录验证方法
7. 自定义提示信息


# 项目资料
1. [后台主题](https://github.com/puikinsh/gentelella)
2. [datatables](http://datatables.club/)
3. [layer](http://layer.layui.com/)
4. [Laravel学院](http://laravelacademy.org/)
5. [http://www.golaravel.com/](http://www.golaravel.com/)
6. [https://packagist.org/](https://packagist.org/)

# 教程资料
1. 项目源码：[laravel-tutorial](https://github.com/lanceWan/laravel-tutorial)
2. 教学笔记：[https://github.com/lanceWan/INote/tree/master/Laravel5-admin](https://github.com/lanceWan/INote/tree/master/Laravel5-admin)