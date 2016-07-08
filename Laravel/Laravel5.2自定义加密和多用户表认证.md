# Laravel5.2自定义加密
> Laravel 为外面提供了开箱即用的 `auth` 认证，只需要一行命令即可实现基本的用户登录注册以及找回密码功能。这些功能都是最基础的，在实际项目中肯定满足不了需求，网上搜索到的资料全部是修改源代码，虽然能实现，却不是一个好的做法。本文将介绍如何优雅的自定义加密方式和多用户表认证，这可能是全网最详细的教程了~

Laravel5自定义登录字段文章:[http://iwanli.me/article/2](http://iwanli.me/article/2 "Laravel5自定义登录字段-i晚黎")

Laravel5自定义登录字段视频:[http://iwanli.me/article/19](http://iwanli.me/article/19 "Laravel5自定义登录字段-i晚黎")

## 自定义加密
总听到小伙伴们说Laravel自带的加密方式不安全，想用自己的加密方式来给用户的密码加密然后存到数据库中，接下来为大家详细讲解注册的时候自定义加密和登录的时候按照我们自己的加密方式进行验证
### 原理解析
登录注册都是通过`AuthController`控制器实现的，在`AuthController`中进行对用户的登录注册引入了 Trait `AuthenticatesAndRegistersUsers` 

**app\Http\Controllers\Auth\AuthController.php**

```php
<?php

namespace App\Http\Controllers\Auth;

use App\User;
use Validator;
use App\Http\Controllers\Controller;
use Illuminate\Foundation\Auth\ThrottlesLogins;
use Illuminate\Foundation\Auth\AuthenticatesAndRegistersUsers;

class AuthController extends Controller
{
    /*
    |--------------------------------------------------------------------------
    | Registration & Login Controller
    |--------------------------------------------------------------------------
    |
    | This controller handles the registration of new users, as well as the
    | authentication of existing users. By default, this controller uses
    | a simple trait to add these behaviors. Why don't you explore it?
    |
    */

    use AuthenticatesAndRegistersUsers, ThrottlesLogins;

    /**
     * Where to redirect users after login / registration.
     *
     * @var string
     */
    protected $redirectTo = '/';

    ....
}
```

我们找到`AuthenticatesAndRegistersUsers.php`

**vendor\laravel\framework\src\Illuminate\Foundation\Auth\AuthenticatesAndRegistersUsers.php**

```php
<?php

namespace Illuminate\Foundation\Auth;

trait AuthenticatesAndRegistersUsers
{
    use AuthenticatesUsers, RegistersUsers {
        AuthenticatesUsers::redirectPath insteadof RegistersUsers;
        AuthenticatesUsers::getGuard insteadof RegistersUsers;
    }
}
```

上面代码轻易发现`AuthenticatesAndRegistersUsers` Trait将用户的登录注册分两个Trait来实现，首先我们只看注册的时候自定义加密，所以先看 `RegistersUsers` Trait，下面讲到用户登录的时候再探讨`AuthenticatesUsers`

**vendor\laravel\framework\src\Illuminate\Foundation\Auth\RegistersUsers.php**

```php
<?php

namespace Illuminate\Foundation\Auth;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

trait RegistersUsers
{
    use RedirectsUsers;

    /**
     * Show the application registration form.
     *
     * @return \Illuminate\Http\Response
     */
    public function getRegister()
    {
        return $this->showRegistrationForm();
    }

    /**
     * Show the application registration form.
     *
     * @return \Illuminate\Http\Response
     */
    public function showRegistrationForm()
    {
        if (property_exists($this, 'registerView')) {
            return view($this->registerView);
        }

        return view('auth.register');
    }

    /**
     * Handle a registration request for the application.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function postRegister(Request $request)
    {
        return $this->register($request);
    }

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

    /**
     * Get the guard to be used during registration.
     *
     * @return string|null
     */
    protected function getGuard()


    {
        return property_exists($this, 'guard') ? $this->guard : null;
    }
}
```

通过上面源码，我们知道在 `RegistersUsers` Trait 中有如下几个方法：

* getRegister()
* showRegistrationForm()
* postRegister()
* register()
* getGuard()

我给大家简单的介绍下每个方法的作用：

**getRegister()**

注册视图，该方法是直接调用 `showRegistrationForm()` 方法

----

**showRegistrationForm()**

获取注册表单方法，该方法中会检测 `registerView` 的变量是否存在，如果不存在则直接调用默认视图文件 `register.blade.php` ，存在则返回变量 `registerView` 指定的视图名称，定义方法可以直接在 `AuthController` 中添加一个变量如：`protected $registerView = 'example'` ，则注册页面指向的视图文件为 `example.blade.php`

----

**postRegister()**

添加用户信息到数据库中，调用 `register()`

----

**register()**

实现注册功能的方法，该方法中包含用户注册信息的验证、登录以及跳转到相关页面方法：

```php
public function register(Request $request)
{	
	/**
	 * 判断用户输入信息
	 */
    $validator = $this->validator($request->all());
	/**
	 * 验证失败会调用这个方法，并返回错误信息并跳转到注册页面
	 * @author 晚黎
	 * @date   2016-07-07T12:29:40+0800
	 */
    if ($validator->fails()) {
        $this->throwValidationException(
            $request, $validator
        );
    }
	/**
	 * 创建用户并登录
	 */
    Auth::guard($this->getGuard())->login($this->create($request->all()));
	/**
	 * 用户登录成功后，跳转到指定页面，自定义在 AuthController 中的  $redirectTo 控制
	 */
    return redirect($this->redirectPath());
}
```

----

**getGuard()**

判断用户是否自定义了 `Guard` ,检测变量 `guard` 是否存在，不存在则返回null。
关于 `Guard` ,我们在下面为大家详细讲解

----

### 实现方法
注册方法(`register()`)中的 `$this->validator($request->all())` 、 `$this->create($request->all())` 调用的 `validator` 和 `create` 方法在这个Trait中并没有找到，所以只有可能是在 `AuthController` 中了，关于Trait的特性，可以看看这篇文章：[http://laravelacademy.org/post/4281.html](http://laravelacademy.org/post/4281.html) ，我们重新看看 `AuthController`

**app\Http\Controllers\Auth\AuthController.php**

```php
<?php

namespace App\Http\Controllers\Auth;

use App\User;
use Validator;
use App\Http\Controllers\Controller;
use Illuminate\Foundation\Auth\ThrottlesLogins;
use Illuminate\Foundation\Auth\AuthenticatesAndRegistersUsers;

class AuthController extends Controller
{
    ...

    /**
     * Get a validator for an incoming registration request.
     *
     * @param  array  $data
     * @return \Illuminate\Contracts\Validation\Validator
     */
    protected function validator(array $data)
    {
        return Validator::make($data, [
            'name' => 'required|max:255',
            'email' => 'required|email|max:255|unique:users',
            'password' => 'required|confirmed|min:6',
        ]);
    }

    /**
     * Create a new user instance after a valid registration.
     *
     * @param  array  $data
     * @return User
     */
    protected function create(array $data)
    {
        return User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => bcrypt($data['password']),
        ]);
    }
}
```

上面 `create()` 方法中，可以看到 `password` 是以 `bcrypt()` 方式加密，所以我们就只需要改为我们自己需要的加密方式即可！例如：`md5($data['password'])`，就是这么简单~

### 小结
如果需要修改Laravel默认的密码加密方式，只需要在 `AuthController` 的 `create()` 方法里面修改 `password` 的加密方式即可！

## 添加自定义Guard

上面修改了密码默认的加密方式，但在登录的时候，Laravel默认的登录验证的时候还是将密码进行 `bcrypt()` 加密。这时候我们就需要添加自定义的 `Guard`，Laravel登录使用的是 `UserProvider` 这个接口。`Illuminate\Contracts\Auth\UserProvider` 实现只负责从持久化存储系统中获取`Illuminate\Contracts\Auth\Authenticatable` 实现类，例如MySQL、Riak等等。这两个接口允许 Laravel 认证机制继续起作用而不管用户数据如何存储或者何种类来展现。

让我们先看看 `Illuminate\Contracts\Auth\UserProvider` 接口：

**UserProvider接口**

```php
<?php
namespace Illuminate\Contracts\Auth;
interface UserProvider {
    public function retrieveById($identifier);
    public function retrieveByToken($identifier, $token);
    public function updateRememberToken(Authenticatable $user, $token);
    public function retrieveByCredentials(array $credentials);
    public function validateCredentials(Authenticatable $user, array $credentials);

}
```

`retrieveById()` 方法通常获取一个代表用户的键，例如 MySQL 数据中的自增ID。该方法获取并返回匹配该ID的Authenticatable 实现类。

`retrieveByToken()` 函数通过唯一标识和存储在 `remember_token` 字段中的“记住我”令牌获取用户。和上一个方法一样，该方法也返回 Authenticatabl 实现类。

`updateRememberToken()` 方法使用新的 token 更新 User表中的 `remember_token` 字段，新令牌可以是新生成的令牌（在登录是选择“记住我”被成功赋值）或者null（用户退出）。

`retrieveByCredentials()` 方法在尝试登录系统时获取传递给 Auth::attempt 方法的认证信息数组。该方法接下来去底层持久化存储系统查询与认证信息匹配的用户，通常，该方法运行一个带“where”条件（$credentials[‘username’]）的查询。然后该方法返回 UserInterface 的实现类。这个方法不做任何密码校验和认证。

`validateCredentials()` 方法比较给定 $user 和 $credentials 来认证用户。例如，这个方法比较 $user->getAuthPassword() 字符串和经 Hash::make 处理的 $credentials['password']。这个方法只验证用户认证信息并返回布尔值。

**Authenticatable接口**

```php
<?php

namespace Illuminate\Contracts\Auth;

interface Authenticatable {

    public function getAuthIdentifier();
    public function getAuthPassword();
    public function getRememberToken();
    public function setRememberToken($value);
    public function getRememberTokenName();

}
```

这个接口很简单，`getAuthIdentifier()` 方法返回用户“主键”，在 MySQL 后台中是ID，`getAuthPassword()` 返回经加密过的用户密码，这个接口允许认证系统处理任何用户类，不管是你使用的是 ORM 还是存储抽象层。默认情况下，Laravel 自带的 app 目录下的 User 类实现了这个接口，所以你可以将这个类作为实现例子。

### 自定义Provider
通过上面解析，我们只需要实现 `UserProvider` 的接口，然后重写 `validateCredentials()` 密码认证方法即可。我们看到 `Illuminate/Contracts/Auth` 下面有两个`UserProvider`的实现，分别为DatabaseUserProvider.php和EloquentUserProvider.php。但是我们验证密码的时候是通过哪个来验证的是在怎么决定的？

官方文档的用户认证这一章，介绍了用户登录的配置正在 `config/auth.php`

```php
'defaults' => [
    'guard' => 'web',
    'passwords' => 'users',
],
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'token',
        'provider' => 'users',
    ],
],
'providers' => [
    'users' => [
        'driver' => 'eloquent',
        'model' => App\User::class, //这是User Model
    ],
],
```

默认情况下调用的`guards` 是 `web` ，而 `web guard` 调用的 `provider` 是 `users` ，`users provider` 默认调用的是 `eloquent` (即用ORM的方式查询用户信息并验证) ，还可以将 `eloquent` 改为 `database`(用Laravel中查询构造器的方式进行查询用户信息并验证)。那么Laravel默认就是通过EloquentUserProvider.php中的retrieveByCredentials来验证的了，我们继续看看具体的方法。

**illuminate/auth/EloquentUserProvider.php**

```php
...

public function retrieveByCredentials(array $credentials)
{
    // First we will add each credential element to the query as a where clause.
    // Then we can execute the query and, if we found a user, return it in a
    // Eloquent User "model" that will be utilized by the Guard instances.
    $query = $this->createModel()->newQuery();

    foreach ($credentials as $key => $value) {
        if (! Str::contains($key, 'password')) {
            $query->where($key, $value);
        }
    }

    return $query->first();
}

/**
 * Validate a user against the given credentials.
 *
 * @param  \Illuminate\Contracts\Auth\Authenticatable  $user
 * @param  array  $credentials
 * @return bool
 */
public function validateCredentials(UserContract $user, array $credentials)
{
    $plain = $credentials['password'];

    return $this->hasher->check($plain, $user->getAuthPassword());
}
...
```
上面两个方法 `retrieveByCredentials` 进行验证用户信息是否存在， 然后 `validateCredentials` 方法通过 `$this->hasher->check` 来将输入的密码和哈希的密码比较来验证密码是否正确，`$plain` 是提交过来的为加密密码字符串，`$user->getAuthPassword()` 是数据库记录存放的加密密码字符串。


OK，看到这里就已经很明显了，要验证自定义的密码只需要重写 `validateCredentials` 方法或者改变 `$this->hasher->check()` 方法的认证代码就行了~

### 方式一(重写validateCredentials)
由于我们只需要重写一个方法，所以不必要实现 `UserProvider.php` 的所有接口，我们就继承 `EloquentUserProvider.php` 并重写 `validateCredentials` 方法就OK了！

**新建app\Repositories\Eloquent\CustomUserProvider.php**

```
<?php
namespace App\Repositories\Eloquent;
use Illuminate\Auth\EloquentUserProvider;
use Illuminate\Contracts\Auth\Authenticatable;
use Illuminate\Support\Str;

class CustomUserProvider extends EloquentUserProvider
{
    /**
     * 重写验证规则，这里我只是简单的MD5来演示，加密的算法完全是根据需要自定义
     * @author 晚黎
     * @date   2016-07-08T11:43:31+0800
     * @param  {[type]}                 Authenticatable $user         [description]
     * @param  {[type]}                 array           $credentials  [description]
     * @return {[type]}                                 [description]
     */
    public function validateCredentials(Authenticatable $user, array $credentials){
    	$plain = $credentials['password'];
    	$check = md5($plain);
    	return $check == $user->getAuthPassword();
    }
}
```

> CustomUserProvider 你可以放到任何地方，我放在repository中

**添加自定义User Providers**

修改 `app/Providers/AuthServiceProvider.php`

```php
<?php

namespace App\Providers;

use Illuminate\Contracts\Auth\Access\Gate as GateContract;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use App\Repositories\Eloquent\CustomUserProvider as Custom;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\Model' => 'App\Policies\ModelPolicy',
    ];

    /**
     * Register any application authentication / authorization services.
     *
     * @param  \Illuminate\Contracts\Auth\Access\Gate  $gate
     * @return void
     */
    public function boot(GateContract $gate)
    {
        $this->registerPolicies($gate);

        $this->app['auth']->provider('custom',function($app, $config)
        {
            return new Custom($this->app['hash'], $config['model']);
        });
    }
}
```

**修改auth配置**

```php
...

'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'customMd5',
    ],

    'api' => [
        'driver' => 'token',
        'provider' => 'users',
    ],
],
'providers' => [
    'users' => [
        'driver' => 'eloquent',
        'model' => App\User::class,
    ],

    'customMd5' => [
        'driver' => 'custom',
        'model' => App\User::class,
    ],
],

...
```

OK，试试用MD5方式登录验证吧~

### 方式二(实现HasherContract接口)
上面通过继承重写 `validateCredentials` 方法实现，现在我们重写 `$this->hasher->check()` 方法，`$this->hasher->check()` 方法是实现了 `Illuminate\Contracts\Hashing\Hasher` 的接口

**Illuminate\Contracts\Hashing\Hasher**

```php
<?php

namespace Illuminate\Contracts\Hashing;

interface Hasher
{
    /**
     * Hash the given value.
     *
     * @param  string  $value
     * @param  array   $options
     * @return string
     */
    public function make($value, array $options = []);

    /**
     * Check the given plain value against a hash.
     *
     * @param  string  $value
     * @param  string  $hashedValue
     * @param  array   $options
     * @return bool
     */
    public function check($value, $hashedValue, array $options = []);

    /**
     * Check if the given hash has been hashed using the given options.
     *
     * @param  string  $hashedValue
     * @param  array   $options
     * @return bool
     */
    public function needsRehash($hashedValue, array $options = []);
}

```

上面 `make()` 是将用户登录的密码进行加密，`check()` 是将用户登录的密码和数据库中的密码进行对比。接下来我们新建一个文件来实现该接口。

**app\Repositories\Eloquent\CustomPassword.php**

```php
<?php
namespace App\Repositories\Eloquent;
use Illuminate\Contracts\Hashing\Hasher as HasherContract;

class CustomPassword implements HasherContract
{
	/**
	 * 加密方式
	 * @author 晚黎
	 * @date   2016-07-08T11:13:35+0800
	 * @param  [type]                   $value   [description]
	 * @param  array                    $options [description]
	 * @return [type]                            [description]
	 */
	public function make($value, array $options = array()) {
        
        return md5($value);
    }

    /**
     * 验证方法
     * @author 晚黎
     * @date   2016-07-08T11:14:11+0800
     * @param  [type]                   $value       [description]
     * @param  [type]                   $hashedValue [description]
     * @param  array                    $options     [description]
     * @return [type]                                [description]
     */
    public function check($value, $hashedValue, array $options = array()) {
        return $this->make($value) === $hashedValue;
    }

    public function needsRehash($hashedValue, array $options = array()) {
        return false;
    }

}
```

> CustomPassword 同样可以放到任何你想要的地方

**注册**
新建 `CustomPasswordProvider`:

```php
<?php
namespace App\Providers;
use Illuminate\Hashing\HashServiceProvider;
use App\Repositories\Eloquent\CustomPassword;

class CustomPasswordProvider extends HashServiceProvider
{
    /**
     * Bootstrap the application services.
     *
     * @return void
     */
    public function boot()
    {
        
    }

    /**
     * Register the application services.
     *
     * @return void
     */
    public function register()
    {
        /**
         * 重写hash
         */
        $this->app->singleton('hash', function() { 
            return new CustomPassword;
        });
    }
}

```

**添加到config/app.php**

```php
'providers' => [
   ...

   App\Providers\CustomPasswordProvider::class,

],
```

OK，登录验证自定义密码大功告成！通过上面我们发现 Laravel 广泛地使用了PHP 的 interface ， 以实现面向接口编程 。组件化的开发扩展更容易，这就是 Laravel 的强大之处。至于如何替换传入的参数 , 则是 Laravel另一个核心理念依赖注入。