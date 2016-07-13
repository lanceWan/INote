# Laravel 5 中使用仓库(Repository)模式

> 首先需要声明的是设计模式和使用的框架以及语言没有任何关系，关键是要理解设计模式背后的原则，这样才能不管你用的是什么技术，都能够在实践中实现相应的设计模式。Repository 是MVC中衔接Controller和Model之间的一个纽带。从概念上讲，Repository 是把将数据给封装后的集合并提供给Controller的操作。

## 简介
Repository 模式将业务逻辑和数据访问分离开，两者之间通过 Repository 接口进行通信，通俗点说，可以把 Repository 看做仓库管理员，我们(Controller)要从仓库取东西（业务逻辑），只需要找管理员要就是了（Repository），不需要自己去找（Model），具体流程如下图所示：

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
    /**
     * 分页
     * @author 晚黎
     * @date   2016-07-13T14:00:15+0800
     * @param  {Number}                 $perPage [description]
     * @param  {[type]}                 $columns [description]
     * @return {[type]}                          [description]
     */
    public function paginate($perPage = 1, $columns = array('*'));
    /**
     * 创建一条新的记录
     * @author 晚黎
     * @date   2016-07-13T14:00:29+0800
     * @param  {[type]}                 array $data         [description]
     * @return {[type]}                       [description]
     */
    public function create(array $data);
    
    public function saveModel(array $data);
    /**
     * 修改数据
     * @author 晚黎
     * @date   2016-07-13T14:00:51+0800
     * @param  {[type]}                 array $data         [description]
     * @param  {[type]}                 $id   [description]
     * @return {[type]}                       [description]
     */
    public function update(array $data, $id);
    /**
     * 删除数据
     * @author 晚黎
     * @date   2016-07-13T14:01:08+0800
     * @param  {[type]}                 $id [description]
     * @return {[type]}                     [description]
     */
    public function delete($id);
    /**
     * 根据主键查找某条记录
     * @author 晚黎
     * @date   2016-07-13T14:01:17+0800
     * @param  {[type]}                 $id      [description]
     * @param  {[type]}                 $columns [description]
     * @return {[type]}                          [description]
     */
    public function find($id, $columns = array('*'));
    /**
     * 根据相关条件查一条记录
     * @author 晚黎
     * @date   2016-07-13T14:01:32+0800
     * @param  {[type]}                 $field   [description]
     * @param  {[type]}                 $value   [description]
     * @param  {[type]}                 $columns [description]
     * @return {[type]}                          [description]
     */
    public function findBy($field, $value, $columns = array('*'));
    /**
     * 根据相关条件查找所有符合记录
     * @author 晚黎
     * @date   2016-07-13T14:01:54+0800
     * @param  {[type]}                 $field   [description]
     * @param  {[type]}                 $value   [description]
     * @param  {[type]}                 $columns [description]
     * @return {[type]}                          [description]
     */
    public function findAllBy($field, $value, $columns = array('*'));
    /**
     * [findWhere description]
     * @author 晚黎
     * @date   2016-07-13T14:02:23+0800
     * @param  {[type]}                 $where   [description]
     * @param  {[type]}                 $columns [description]
     * @return {[type]}                          [description]
     */
    public function findWhere($where, $columns = array('*'));
}
```

## 目录结构

