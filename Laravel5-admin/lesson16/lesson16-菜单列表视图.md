# Laravel5.2实战视频之菜单列表
> 晚黎 交流群：312621686  博客地址：[http://www.iwanli.me](http://www.iwanli.me "i晚黎的博客")

![交流群](https://github.com/lanceWan/INote/blob/master/Laravel5-admin/asssets/Laravel%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png "交流群二维码")


# 菜单列表视图

## 获取菜单列表方法改进

```php

public function sortMenuSetCache($menus)

{

	$menus = $this->model->orderBy('sort','desc')->get()->toArray();

	if ($menus) {

		$menuList = $this->sortMenu($menus);

		foreach ($menuList as $key => &$v) {

			if ($v['child']) {

				$sort = array_column($v['child'], 'sort');

				array_multisort($sort,SORT_DESC,$v['child']);

			}

		}

		return $menuList;

	}

	return '';

}

```

## 菜单列表视图展示（Presenter）

最大限度的复用方法

```php

    /**

	 * 返回菜单HTML代码

	 * @author 晚黎

	 * @date   2016-08-10

	 * @param  [type]     $id    [description]

	 * @param  [type]     $name  [description]

	 * @param  [type]     $child [description]

	 * @return [type]            [description]

	 */

	protected function getNestableItem($id,$name,$child)

	{

		if ($child) {

			return $this->getHandleList($id,$name,$child);

		}

		return '<li class="dd-item dd3-item" data-id="'.$id.'"><div class="dd-handle dd3-handle"> </div><div class="dd3-content"> '.$name.' </div></li>';

	}

	/**

	 * 判断是否有子集

	 * @author 晚黎

	 * @date   2016-08-10

	 * @param  [type]     $id    [description]

	 * @param  [type]     $name  [description]

	 * @param  [type]     $child [description]

	 * @return [type]            [description]

	 */

	protected function getHandleList($id,$name,$child)

	{

		$handle = '<li class="dd-item dd3-item" data-id="'.$id.'"><div class="dd-handle dd3-handle"> </div><div class="dd3-content"> '.$name.' </div><ol class="dd-list">';

		foreach ($child as $v) {

			$handle .= $this->getNestableItem($v['id'],$v['name'],$v['child']);

		}

		$handle .= '</ol></li>';

		return $handle;

	}

```

对于没有用Presenter模式的代码明显的看出来优势：

```

<div class="dd" id="nestable_list">

  <ol class="dd-list">

      @if($menus)

      @foreach($menus as $v)

      @if($v['child'])

      <li class="dd-item" data-id="{{$v['id']}}" data-pid="{{$v['pid']}}">

        <div class="dd-handle dd3-handle"></div>

          <div class="dd3-content">

            {{$v['name']}}

            <div class="pull-right action-buttons">

            @permission(config('admin.permissions.menu.create'))

            <a href="javascript:;" data-pid="{{$v['id']}}" class="btn-xs tooltips createMenu" data-original-title="{{trans('crud.create')}}"  data-placement="top"><i class="fa fa-plus"></i></a>

            @endpermission

            @permission(config('admin.permissions.menu.edit'))

            <a href="javascript:;" data-href="{{url('admin/menu/'.$v['id'].'/edit')}}" class="btn-xs tooltips editMenu" data-original-title="{{trans('crud.edit')}}"  data-placement="top"><i class="fa fa-pencil"></i></a>

            @endpermission

            @permission(config('admin.permissions.menu.destory'))

            <a href="javascript:;" data-id="{{$v['id']}}" class="btn-xs tooltips destoryMenu" data-original-title="{{trans('crud.destory')}}"  data-placement="top"><i class="fa fa-trash"></i><form action="{{url('admin/menu/'.$v['id'])}}" method="POST" name="delete_item{{$v['id']}}" style="display:none"><input type="hidden" name="_method" value="delete"><input type="hidden" name="_token" value="{{csrf_token()}}"></form></a>

            @endpermission

            </div>

          </div>

          <ol class="dd-list">

              @foreach($v['child'] as $val)

              <li class="dd-item" data-id="{{$val['id']}}" data-pid="{{$val['pid']}}">

                <div class="dd-handle dd3-handle"></div>

                <div class="dd3-content">

                  {{$val['name']}}

                  <div class="pull-right action-buttons">

                  @permission(config('admin.permissions.menu.edit'))

                  <a href="javascript:;" data-href="{{url('admin/menu/'.$val['id'].'/edit')}}" class="btn-xs tooltips editMenu" data-original-title="{{trans('crud.edit')}}"  data-placement="top"><i class="fa fa-pencil"></i></a>

                  @endpermission

                  @permission(config('admin.permissions.menu.destory'))

                  <a href="javascript:;" data-id="{{$val['id']}}" class="btn-xs tooltips destoryMenu" data-original-title="{{trans('crud.destory')}}"  data-placement="top"><i class="fa fa-trash"></i><form action="{{url('admin/menu/'.$val['id'])}}" method="POST" name="delete_item{{$val['id']}}" style="display:none"><input type="hidden" name="_method" value="delete"><input type="hidden" name="_token" value="{{csrf_token()}}"></form></a>

                  @endpermission

                  </div>

                </div>

              </li>

              @endforeach

          </ol>

      </li>

      @else

          <li class="dd-item" data-id="{{$v['id']}}" data-pid="{{$v['pid']}}">

              <div class="dd-handle dd3-handle"></div>

              <div class="dd3-content"> 

                {{$v['name']}} 

                <div class="pull-right action-buttons">

                @permission(config('admin.permissions.menu.create'))

                <a href="javascript:;" data-pid="{{$v['id']}}" class="btn-xs tooltips createMenu" data-original-title="{{trans('crud.create')}}"  data-placement="top"><i class="fa fa-plus"></i></a>

                @endpermission

                @permission(config('admin.permissions.menu.edit'))

                <a href="javascript:;" data-href="{{url('admin/menu/'.$v['id'].'/edit')}}" class="btn-xs tooltips editMenu" data-original-title="{{trans('crud.edit')}}"  data-placement="top"><i class="fa fa-pencil"></i></a>

                @endpermission

                @permission(config('admin.permissions.menu.destory'))

                <a href="javascript:;" data-id="{{$v['id']}}" class="btn-xs tooltips destoryMenu" data-original-title="{{trans('crud.destory')}}"  data-placement="top"><i class="fa fa-trash"></i><form action="{{url('admin/menu/'.$v['id'])}}" method="POST" name="delete_item{{$v['id']}}" style="display:none"><input type="hidden" name="_method" value="delete"><input type="hidden" name="_token" value="{{csrf_token()}}"></form></a>

                @endpermission

                </div>

            </div>

          </li>

      @endif

      @endforeach

      @endif

  </ol>

</div>

```



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