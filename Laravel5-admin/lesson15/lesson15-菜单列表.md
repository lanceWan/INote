# Laravel5.2实战视频之菜单列表
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")


# 菜单数据递归排序

1. 递归菜单数据

```php

public function sortMenu($menus,$pid = 0)

{

 $arr = [];

 if (empty($menus)) {

     return '';

 }

 foreach ($menus as $k => $v) {

     if ($v['parent_id'] == $pid) {

         $arr[$k] = $v;

         $arr[$k]['child'] = self::sortMenu($menus,$v->id);

     }

 }

 return $arr;

}

```

1. 对子菜单进行排序

```php

//子菜单进行排序

foreach ($menuList as &$v) {

 if ($v['child']) {

 $sort = array_column($v['child'],'sort');

 array_multisort($sort,SORT_DESC,$v['child']);

 }

}

```

**array\_column** — 返回数组中指定的一列

```php

<?php

// Array representing a possible record set returned from a database

$records = array(

 array(

 'id' => 2135,

 'first_name' => 'John',

 'last_name' => 'Doe',

 ),

 array(

 'id' => 3245,

 'first_name' => 'Sally',

 'last_name' => 'Smith',

 ),

 array(

 'id' => 5342,

 'first_name' => 'Jane',

 'last_name' => 'Jones',

 ),

 array(

 'id' => 5623,

 'first_name' => 'Peter',

 'last_name' => 'Doe',

 )

);

$first_names = array_column($records, 'first_name');

print_r($first_names);

?>

```

以上例程会输出：

`Array ( [0] => John [1] => Sally [2] => Jane [3] => Peter )`

**array\_multisort** — 对多个数组或多维数组进行排序

`bool array_multisort ( array &$arr [, mixed $arg = SORT_ASC [, mixed $arg = SORT_REGULAR [, mixed $... ]]] )`

排序顺序标志：

* SORT\_ASC - 按照上升顺序排序
* SORT\_DESC - 按照下降顺序排序

排序类型标志：

* SORT\_REGULAR - 将项目按照通常方法比较
* SORT\_NUMERIC - 将项目按照数值比较
* SORT\_STRING - 将项目按照字符串比较

# 项目资料
1. [后台主题](https://github.com/puikinsh/gentelella)
2. [datatables](http://datatables.club/)
3. [layer](http://layer.layui.com/)
4. [Laravel学院](http://laravelacademy.org/)
5. [http://www.golaravel.com/](http://www.golaravel.com/)
6. [https://packagist.org/](https://packagist.org/)
7. [Laravel多语言](https://github.com/caouecs/Laravel-lang)
8. [Redis](https://github.com/MSOpenTech/redis)
9. [nestable](https://github.com/dbushell/Nestable)
10. [flash](https://packagist.org/packages/laracasts/flash)

# 教程资料
1. 项目源码：[laravel-tutorial](https://github.com/lanceWan/laravel-tutorial)
2. 教学笔记：[https://github.com/lanceWan/INote/tree/master/Laravel5-admin](https://github.com/lanceWan/INote/tree/master/Laravel5-admin)