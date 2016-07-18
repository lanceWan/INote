# Laravel5.2实战视频之Entrust安装
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

# Entrust权限控制简介

![Entrust权限控制简介](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/20160715114617.png)

# redis包安装
`composer require predis/predis 1.0.*`

[http://laravelacademy.org/post/1839.html](http://laravelacademy.org/post/1839.html)

php扩展相关解释

# 数据填充

创建权限填充数据、角色数据。在角色数据模型中添加角色与权限之间的关系。最后在用户模型中添加用户与角色之间的关系。

# 使用方法
```
// 判断角色
$user->hasRole('owner');   // false
$user->hasRole('admin');   // true
// 判断权限
$user->can('edit-user');   // false
$user->can('create-post'); // true

// 多个角色或权限判断（满足其中一个即可，第二个可选参数，true时需要同时满足）
$user->hasRole(['owner', 'admin']);       // true  
$user->can(['edit-user', 'create-post']); // true

// 判断角色和权限其他用法
Entrust::hasRole('role-name');
Entrust::can('permission-name');

// is identical to

Auth::user()->hasRole('role-name');
Auth::user()->can('permission-name);

// 匹配判断
// match any admin permission
$user->can("admin.*"); // true

// match any permission about users
$user->can("*_users"); // true

// 用户能力判断
$user->ability(array('admin', 'owner'), array('create-post', 'edit-user'));

// or

$user->ability('admin,owner', 'create-post,edit-user');

// 第三个可选参数为数组
$options = array(
    'validate_all' => true | false (Default: false),
    'return_type'  => boolean | array | both (Default: boolean)
);

$options = array(
    'validate_all' => true,
    'return_type' => 'both'
);

list($validate, $allValidations) = $user->ability(
    array('admin', 'owner'),
    array('create-post', 'edit-user'),
    $options
);

var_dump($validate);
// bool(false)

var_dump($allValidations);
// array(4) {
//     ['role'] => bool(true)
//     ['role_2'] => bool(false)
//     ['create-post'] => bool(true)
//     ['edit-user'] => bool(false)
// }

// 等价写法

Entrust::ability('admin,owner', 'create-post,edit-user');

// is identical to

Auth::user()->ability('admin,owner', 'create-post,edit-user');

```

**模板标签**

```
@role('admin')
    <p>This is visible to users with the admin role. Gets translated to 
    \Entrust::role('admin')</p>
@endrole

@permission('manage-admins')
    <p>This is visible to users with the given permissions. Gets translated to 
    \Entrust::can('manage-admins'). The @can directive is already taken by core 
    laravel authorization package, hence the @permission directive instead.</p>
@endpermission

@ability('admin,owner', 'create-post,edit-user')
    <p>This is visible to users with the given abilities. Gets translated to 
    \Entrust::ability('admin,owner', 'create-post,edit-user')</p>
@endability
```

**中间件**

```
Route::group(['prefix' => 'admin', 'middleware' => ['role:admin']], function() {
    Route::get('/', 'AdminController@welcome');
    Route::get('/manage', ['middleware' => ['permission:manage-admins'], 'uses' => 'AdminController@manageAdmins']);
});
```

多个参数：

```
'middleware' => ['role:admin|root']

'middleware' => ['permission:owner', 'permission:writer']

'middleware' => ['ability:admin|owner,create-post|edit-user,true']
```

**路由过滤**



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