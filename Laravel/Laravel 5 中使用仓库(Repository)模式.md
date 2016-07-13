# Laravel 5 中使用仓库(Repository)模式

> 首先需要声明的是设计模式和使用的框架以及语言没有任何关系，关键是要理解设计模式背后的原则，这样才能不管你用的是什么技术，都能够在实践中实现相应的设计模式。Repository 是MVC中衔接Controller和Model之间的一个纽带。从概念上讲，Repository 是把将数据给封装后的集合并提供给Controller的操作。

## 简介
Repository 模式将业务逻辑和数据访问分离开，两者之间通过 Repository 接口进行通信，通俗点说，可以把 Repository 看做仓库管理员，我们(Controller)要从仓库取东西（业务逻辑），只需要找管理员要就是了（Repository），不需要自己去找（Model），具体流程如下图所示：

![https://github.com/lanceWan/INote/blob/master/Laravel/assets/Respository%E5%8E%9F%E7%90%86%E5%9B%BE.png](https://github.com/lanceWan/INote/blob/master/Laravel/assets/Respository%E5%8E%9F%E7%90%86%E5%9B%BE.png)

这种将数据访问从业务逻辑中分离出来的模式有很多好处：

1. 集中数据访问逻辑使代码易于维护
2. 业务和数据访问逻辑完全分离
3. 减少重复代码
4. 使程序出错的几率降低

一般情况下，我们会做这些事情:

* 获取所有记录
* 获取分页记录
* 创建一条新的记录
* 通过主键获取指定记录
* 通过属性获取相应记录
* 更新一条记录
* 删除一条记录

现在你已经意识到如果我们为每个数据对象实现这些操作要编写多少重复代码！当然，对小型项目而言，这不是什么大问题，但如果对大型应用而言，这显然是个坏主意。现在我们要定义这些重复的操作，需要创建一个 Repository 接口：

```php
<?php
namespace App\Repositories\Contracts;
interface RepositoryInterface {
	/**
	 * 查找所有
	 * @author 晚黎
	 * @date   2016-07-13T14:00:03+0800
	 * @param  {[type]}                 $columns [description]
	 * @return {[type]}                          [description]
	 */
    public function all($columns = array('*'));
    
}
```

## 目录结构
在创建具体的 Repository 实现类之前，让我们先想想要如何组织我们要编写的代码，通常，当我要创建一些类的时候，我喜欢以组件的方式来组织代码，因为我希望这些代码可以很方便地在其他项目中被复用。我为 Repository 组件定义的目录结构如下：

![https://github.com/lanceWan/INote/blob/master/Laravel/assets/20160713140409.png](https://github.com/lanceWan/INote/blob/master/Laravel/assets/20160713140409.png)

这是只是我的代码规范，大家可以按照自己的理解来。在 `src` 目录下，我创建了三个子目录：`Contracts`、`Eloquent` 和 `Exceptions`。这样命令的原因是显而易见的，一眼就能看出里面存放的是什么类。我们会将接口放在 `Contracts` 目录下，`Eloquent` 目录用于存放实现 `Repository` 接口的抽象类和具体类，而 `Exceptions` 目录用于存放异常处理类。

## Repository 实现

![https://github.com/lanceWan/INote/blob/master/Laravel/assets/Respository%E5%AE%9E%E7%8E%B0.png](https://github.com/lanceWan/INote/blob/master/Laravel/assets/Respository%E5%AE%9E%E7%8E%B0.png)

每一个具体的子 `Repository` 都继承自抽象的 `Repository` 父类，这个抽像的 `Repository` 父类则实现了 `RepositoryInterface` 接口。现在，我们正式开始实现这个接口。

```php
<?php
namespace App\Repositories\Eloquent;
use App\Repositories\Contracts\RepositoryInterface;
use App\Repositories\Exceptions\RepositoryException;
use Illuminate\Container\Container as App;
use Illuminate\Database\Eloquent\Model;
abstract class Repository implements RepositoryInterface{
    
    private $app;
    
    private $model;

    public function __construct(App $app)
    {
        $this->app = $app;
        $this->makeModel();
    }

    /**
     * 定义了一个抽象方法 model()，强制在实现类中实现该方法已获取当前实现类对应的模型
     *
     * @return mixed
     */
    public abstract function model();

    /**
     * 抽象类中实现接口的所有方法
     */
    
    public function all($columns = array('*'))
    {
        return $this->model->all($columns);
    }
    
    /**
     * 获取model对象
     * @author 晚黎
     * @date   2016-07-13T14:50:55+0800
     * @return {[type]}                 [description]
     */
    public function makeModel() {
        $model = $this->app->make($this->model());

        if (!$model instanceof Model){
            throw new RepositoryException("Class {$this->model()} must be an instance of Illuminate\\Database\\Eloquent\\Model");
        }

        return $this->model = $model;
   }
}
```

定义了抽象类之后，我们其他的 `Repository` 继承这个基类。

```php
<?php
namespace App\Repositories\Eloquent;
use App\Repositories\Eloquent\Repository;
class HomeRepository extends Repository
{

    public function model()
    {
        return 'App\User';
    }

    public function test()
    {
        echo "string11111";
    }
}
```

现在剩下唯一要做的就是在 HomeController 中依赖注入 HomeRepository：

```php
<?php
namespace App\Http\Controllers;
use App\Http\Requests;
use Illuminate\Http\Request;
use App\Repositories\Eloquent\HomeRepository as Home;

class HomeController extends Controller
{

    private $home;
    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct(Home $home)
    {
        $this->home = $home;
    }

    /**
     * Show the application dashboard.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        dd($this->home->all());
        dd($this->home->test());
    }
}
```

OK，最简单的 Repository 模式就介绍到这里了，其实还有更高级的封装，但是我觉得那样做并没有减少什么代码，反而更复杂且不容易理解 。