# Laravel5.2实战视频之用户登录
> 晚黎 交流群：312621686



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