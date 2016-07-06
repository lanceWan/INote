# Laravel5.2实战视频之自定义登录
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

#为auth更换自定义页面
> 上节 `php artisan make:auth` 命令还会在routes中添加路由

asset默认指向的是public文件夹

**CSRF 攻击**
跨站请求伪造是一种通过伪装授权用户的请求来利用授信网站的恶意漏洞

`{{ csrf_field() }}` 和辅助函数 `{{csrf_token()}}`



**从 CSRF 保护中排除指定 URL**
要实现这一目的，你需要在 VerifyCsrfToken 中间件中将要排除的 URL 添加到 $except 属性:

**Ajax 设置CSRF-Token**
页面设置head中设置：
```
<meta name="csrf-token" content="{{ csrf_token() }}">
```
基于 AJAX 的应用提供了简单、方便的方式来避免 CSRF 攻击：
```
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});
```

> 文档位置：[http://laravelacademy.org/post/2784.html#csrf-attack](http://laravelacademy.org/post/2784.html#csrf-attack "CSRF 攻击")

# 自定义登录字段

根据 `AuthenticatesUsers` Trait 中的方法
```
/**
 * Get the login username to be used by the controller.
 *
 * @return string
 */
public function loginUsername()
{
    return property_exists($this, 'username') ? $this->username : 'email';
}
```

**将自定义字段改为配置**
原理详解：[Laravel5 自定义登录字段](http://iwanli.me/article/2 "Laravel5 自定义登录字段")

# 讲自定义登录字段在注册中显示
根据 `RegistersUsers` [Trait](http://laravelacademy.org/post/4281.html "现代 PHP 新特性系列（三） —— Trait 概览") 中的方法：
```
/**
 * Handle a registration request for the application.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return \Illuminate\Http\Response
 */
public function register(Request $request)
{
    $validator = $this->validator($request->all());

    if ($validator->fails()) {
        $this->throwValidationException(
            $request, $validator
        );
    }

    Auth::guard($this->getGuard())->login($this->create($request->all()));

    return redirect($this->redirectPath());
}
```

> 灵活的配置使Laravel强大的原因


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