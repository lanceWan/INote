# Laravel扩展包Entrust踩过的坑
> 记录一下使用entrust遇到的问题和解决办法

## migrate数据迁移报错

composer拉下来代码后，生成数据迁移文件后执行`php artisan migrate` 的时候报错：

```
$ php artisan migrate
Migration table created successfully.

  [Illuminate\Database\QueryException]

  SQLSTATE[42000]: Syntax error or access violation: 1103 Incorrect table name '' (SQL: alter table `r
ole_user` add constraint role_user_user_id_foreign foreign key (`user_id`) references `` (`id`) on delete
 cascade on update cascade)

  [PDOException]
  SQLSTATE[42000]: Syntax error or access violation: 1103 Incorrect table name ''
```

**解决方案**

在迁移文件 `database/migrations/_entrust_setup_tables.php ,the code what create role_user table` 修改一下 :
```
$table->foreign('user_id')->references('id')->on('')->onUpdate('cascade')->onDelete('cascade');

```
修改为：
```php
$table->foreign('user_id')->references('id')->on('users')->onUpdate('cascade')->onDelete('cascade');
```

