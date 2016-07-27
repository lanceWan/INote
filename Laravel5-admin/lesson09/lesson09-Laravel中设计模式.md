# Laravel5.2实战视频之设计模式

> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")

首先需要声明的是设计模式和使用的框架以及语言没有任何关系，关键是要理解设计模式背后的原则，这样才能不管你用的是什么技术，都能够在实践中实现相应的设计模式。

# 目录规范
在 `app` 目录下建立一个 `Repositories` 目录， 在 `Repositories` 目录下，我创建了三个子目录：`Contracts`、`Eloquent` 和 `Exceptions`。我们会将接口放在 `Contracts` 目录下，`Eloquent` 目录用于存放实现 `Repository` 接口的抽象类和具体类，而 `Exceptions` 目录用于存放异常处理类。这个规范不是强制性的，没有对错。如果你有更好的理解，完全可按照不同方式来进行设计。

# 服务模式
这种模式是参考Laravel的服务容器和服务提供者，Laravel 服务容器是一个用于管理类依赖和执行依赖注入的强大工具。依赖注入听上去很花哨，其实质是通过构造函数或者某些情况下通过 set 方法将类依赖注入到类中。服务提供者是所有Laravel应用启动的中心，你自己的应用以及所有Laravel的核心服务都是通过服务提供者启动。通过服务容器创建服务(service)，然后在服务提供者中(provider)注册。


**定义接口**

```
<?php
namespace App\Repositories\Contracts;
interface UserInterface{

	/**
	 * 获取所有用户
	 * @author 晚黎
	 * @date   2016-07-21
	 * @return [type]     [description]
	 */
	public function allUsers();

}
```

**实现接口类**
```
<?php
namespace App\Repositories\Eloquent;
use App\Repositories\Contracts\UserInterface;
use App\User;
/**
* 服务模式
*/
class UserServiceRepository implements UserInterface
{
	
	public function allUsers()
	{
		return User::all()->toArray();
	}
}
```

**注册服务**

* 创建provider或在系统的provider中注册

```
public function register()
{
    // 单例
    // $this->app->singleton('App\Repositories\Contracts\UserInterface', function ($app) {
    //     return new \App\Repositories\Eloquent\UserServiceRepository();
    // });

    // 绑定
    $this->app->bind('App\Repositories\Contracts\UserInterface', 'App\Repositories\Eloquent\UserServiceRepository');
}
```

* 添加到配置文件中

```
App\Providers\RepositoryServiceProvider::class,
```


**调用**

```
use App\Repositories\Contracts\UserInterface;

...

public function __construct(UserInterface $user)
{
    $this->user = $user;
}

public function index()
{
    dd($this->user->allUsers());
}
```

# 门面模式

门面为应用的服务容器中的绑定类提供了一个“静态”接口。Laravel 内置了很多门面，你可能在不知道的情况下正在使用它们。Laravel 的门面作为服务容器中的底层类的“静态代理”，相比于传统静态方法，在维护时能够提供更加易于测试、更加灵活的、简明且富有表现力的语法。门面就是一个提供访问容器中对象的类。该机制原理由 Facade 类实现，Laravel 自带的门面，以及创建的自定义门面，都会继承自 Illuminate\Support\Facades\Facade 基类。门面类只需要实现一个方法：getFacadeAccessor。正是 getFacadeAccessor 方法定义了从容器中解析什么，然后 Facade 基类使用魔术方法 __callStatic() 从你的门面中调用解析对象。

**定义接口(建议)**
虽然门面模式中可以不用定义接口，个人建议还是定义接口。在以后的项目功能升级并兼容老功能的时候非常有用，增加了开发时间，却减少了后期维护或升级功能的难度。

```
<?php
namespace App\Repositories\Contracts;
interface UserInterface{

	/**
	 * 获取所有用户
	 * @author 晚黎
	 * @date   2016-07-21
	 * @return [type]     [description]
	 */
	public function allUsers();

}
```

**接口实现类**

```
<?php
namespace App\Repositories\Eloquent;
use App\Repositories\Contracts\UserInterface;
use App\User;
/**
* 门面模式
*/
class UserFacadeRepository implements UserInterface
{
	
	public function allUsers()
	{
		return User::all()->toArray();
	}
}
```

**注册**

```
public function register()
{
    // 单例
    $this->app->singleton('UserFacadeRepository', function ($app) {
        return new \App\Repositories\Eloquent\UserFacadeRepository();
    });
}
```

**定义Facade**

* 在 `app` 目录下创建 Facades目录
* 定义Facade

```
<?php
namespace App\Facades;
use Illuminate\Support\Facades\Facade;
class UserFacade extends Facade{
    /**
     * 获取组件注册名称
     *
     * @return string
     */
    protected static function getFacadeAccessor() { 
        return 'UserFacadeRepository'; 
    }
}
```


**添加Facade**

```
'UserFacadeRepo' => App\Facades\UserFacade::class,
```

**使用**

```
use UserFacadeRepo;

...

public function index()
{   
    dd(UserFacadeRepo::allUsers());
}
```

# 仓库(Repository)模式

Repository 是衔接数据映射层和领域层之间的一个纽带，作用相当于一个在内存中的域对象集合。客户端对象把查询的一些实体进行组合，并把它 们提交给 Repository。对象能够从 Repository 中移除或者添加。Repository 是MVC中衔接Controller和Model之间的一个纽带。从概念上讲，Repository 是把将数据给封装后的集合并提供给Controller的操作。

> 参考文章：[https://bosnadev.com/2015/03/07/using-repository-pattern-in-laravel-5/](https://bosnadev.com/2015/03/07/using-repository-pattern-in-laravel-5/)
[http://laravelacademy.org/post/3063.html](http://laravelacademy.org/post/3063.html)


**定义接口**

```
<?php
namespace App\Repositories\Contracts;
interface UserInterface{

	/**
	 * 获取所有用户
	 * @author 晚黎
	 * @date   2016-07-21
	 * @return [type]     [description]
	 */
	public function allUsers();

}
```
**定义一个基本的抽象类**

```
<?php
namespace App\Repositories\Eloquent;
use App\Repositories\Contracts\RepositoryInterface;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Container\Container as App;
abstract class Repository implements RepositoryInterface{

	/*App容器*/
	protected $app;

	/*操作model*/
	protected $model;

    public function __construct(App $app) {
        $this->app = $app;
        $this->makeModel();
    }

    abstract function model();


    /**
     * 统计数量
     * @author 晚黎
     * @date   2016-07-21T17:09:32+0800
     * @param  string                   $field   [description]
     * @param  string                   $value   [description]
     * @param  array                    $columns [description]
     * @return [type]                            [description]
     */
    public function count($field='',$value='',$columns = array('*')){
    	if (is_array($field) && is_array($value)) {
    		$where = array_combine($field,$value);
    		$this->model = $this->model->where($where);
    	}
    	if (is_string($field) && is_string($value)) {
    		$this->model = $this->model->where($field,$value);
    	}
		return $this->model->select($columns)->count();
    }

    public function makeModel(){
    	$model = $this->app->make($this->model());
    	/*是否是Model实例*/
    	if (!$model instanceof Model){
    		throw new RepositoryException("Class {$this->model()} must be an instance of Illuminate\\Database\\Eloquent\\Model");
    	}
    	$this->model = $model;
    }

	
}
```

**创建Repository并继承抽象类**

**控制器中依赖注入**


# 总结

这种将数据访问从业务逻辑中分离出来的模式有很多好处：

1. 集中数据访问逻辑使代码易于维护
2. 业务和数据访问逻辑完全分离
3. 减少重复代码
4. 使程序出错的几率降低

> 一个控制器调用多个Repository数据的时候，要么都在相应的Repository中进行注入或引用，要么在控制其中添加。避免交叉引用乱象!