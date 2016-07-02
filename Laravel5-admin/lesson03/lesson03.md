# Laravel5.2实战视频之用户登录
> 晚黎 交流群：312621686

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

## Faker填充数据应用
[https://github.com/fzaninotto/Faker](https://github.com/fzaninotto/Faker)

通常调用方式：
```
DB::table('users')->insert([
    'name' => str_random(10),
    'email' => str_random(10).'@gmail.com',
    'password' => bcrypt('secret'),
]);
```

使用模型工厂Faker
```
factory('App\User', 50)->create()->each(function($u) {
    $u->posts()->save(factory('App\Post')->make());
});
```

## Laravel5.2中默中用户登录
Laravel 中实现用户认证非常简单。实际上，几乎所有东西都已经为你配置好了。配置文件位于`config/auth.php`，其中包含了用于调整认证服务行为的、文档友好的选项配置。
### 路由
Laravel 通过运行如下命令可快速生成认证所需要的路由和视图:
```
php artisan make:auth
```
### 视图
正如上面所提到的，`php artisan make:auth` 命令会在 `resources/views/auth` 目录下创建所有认证需要的视图。

`make:auth` 命令还创建了 `resources/views/layouts` 目录，该目录下包含了应用的基础布局文件。所有这些视图都使用了 `Bootstrap CSS` 框架，你也可以根据需要对其进行自定义。

## 自定义登录页面样式