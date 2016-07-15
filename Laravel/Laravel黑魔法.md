# Laravel 黑魔法 - 分享Laravel小技巧

## 简单的输出 “N 分钟前”、“1 天前” 等友好的 time ago 时间
文章地址：[http://iwanli.me/article/9](http://iwanli.me/article/9)

## laravel 属性的set和get方法
Laravel为数据库表中的每个字段都有默认的 `set` 和 `get` 方法。你可以在 Model 中任意定义获取数据的格式以及插入数据库之前修改数据。

**定义一个get**

语法定义一个get `getFooAttribute()` Foo是大写的属性你想访问的数据。如果你的属性是first_name，则方法名为 `getFirstNameAttribute()`，你需要定义一个访问器函数如下：

```
public function getFirstNameAttribute($value)
{
    return ucfirst($value);
}
```

**定义一个set**

```php
public function setFirstNameAttribute($value)
{
	$this->attributes['first_name'] = ucfirst($value);
}
```

**简单示例**
有时候数据库中的某些字段是以json形式存储的，每次存储或获取的时候都要执行 `json_encode()` 、`json_decode`方法。现在有了这个属性就可以轻松完成重复的事情。

```
PHP

/**
 * Make sure that we get an array from JSON string 
 *
 * @param $value
 * @return array
 */
public function getSettingsAttribute($value) {
    return json_decode($value, true);
}


/**
 * Encode an array to a JSON string
 * 
 * @param $value
 */
public function setSettingsAttribute($value)
{
    $this->attributes['settings'] = json_encode($value);
}

```