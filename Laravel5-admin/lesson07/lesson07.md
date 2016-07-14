# Laravel5.2实战视频之Entrust安装
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

# 权限介绍
* ALC模型

ACL是最早也是最基本的一种访问控制机制，它的原理非常简单：每一项资源，都配有一个列表，这个列表记录的就是哪些用户可以对这项资源执行CRUD中的那些操作。当系统试图访问这项资源时，会首先检查这个列表中是否有关于当前用户的访问权限，从而确定当前用户可否执行相应的操作。总得来说，ACL是一种面向资源的访问控制模型，它的机制是围绕“资源”展开的。

* RBAC模型

RBAC是把用户按角色进行归类，通过用户的角色来确定用户能否针对某项资源进行某项操作。RBAC相对于ACL最大的优势就是它简化了用户与权限的管理，通过对用户进行分类，使得角色与权限关联起来，而用户与权限变成了间接关联。RBAC模型使得访问控制，特别是对用户的授权管理变得非常简单和易于维护，因此有广泛的应用。

* 其他

[https://github.com/romanbican/roles](https://github.com/romanbican/roles)

# entrust安装
1. Packageist上搜索entrust
2. 查看安装文档或去 [github主页](https://github.com/Zizaco/entrust) 查看
3. composer加载包 `composer require zizaco/entrust:5.2.x-dev` 或在 `composer.json` 中加入 `"zizaco/entrust": "5.2.x-dev"` 然后执行 `composer install`
4. 添加provider `Zizaco\Entrust\EntrustServiceProvider::class,`
5. 添加facade `'Entrust'   => Zizaco\Entrust\EntrustFacade::class,`
6. 中间件添加（需要） 
7. 生成迁移文件 `php artisan entrust:migration`
8. 生成表结构 `php artisan migrate` 此处有坑具体解决办法[https://github.com/lanceWan/INote/blob/master/Laravel/Laravel%E6%89%A9%E5%B1%95%E5%8C%85Entrust%E8%B8%A9%E8%BF%87%E7%9A%84%E5%9D%91.md](https://github.com/lanceWan/INote/blob/master/Laravel/Laravel%E6%89%A9%E5%B1%95%E5%8C%85Entrust%E8%B8%A9%E8%BF%87%E7%9A%84%E5%9D%91.md)
9. 创建Model并继承相应的Model
10. 文件加载 `composer dump-autoload`

# Redis安装
现在版本的entrust依赖于缓存，但Laravel默认的文件缓存是不支持的，用的时候会报错 ，所以必须安装Redis或Memcache，这里我使用Redis，关于 Redis的使用或基本命令可以看我博客的Reids基础学习笔记：[http://iwanli.me/cate/6](http://iwanli.me/cate/6 "从零开始学Redis笔记-晚黎")

**Windows安装**

这里只介绍Windows安装，其他平平台可以到群里互相交流。

**Redis客户端**

[RedisDesktopManager](http://redisdesktop.com/)


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