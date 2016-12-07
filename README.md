# wx-mp-doc
## 条件渲染
### wx:if

~~~ xml
  <view wx:if="{{name}}">{{name}}</view>
~~~

* 当name是null,0,false，特别是空字符串''时，这些js判断为假的值wx:if也判断为假。
__Tip:__ 此时的行为和js相同。

~~~ xml
  <view wx:if="{{obj.val.name}}">{{name}}</view>
~~~

* 当obj不存在的时候，即：obj===undefined。不管引用了多少层。wx:if什么都不做，并不出错。
__Tip:__ 此时的行为和js不同，不过极可能正是你想要的效果。

## 列表渲染
### wx:for
~~~ xml
  <block wx:for="{{obj}}"><view>{{item}}</view></block>
~~~
* 如果obj是null, '', false等js认为的空，什么都不做
* 如果obj是{},[]空对象，什么都不做
* 如果是字符串，则每个字符为一个元素
* 如果是个对象，则输出value，item为value。比如{id:1, name:'abc'}，输出1和abc
* 如果是个数组，则输出每个元素，item为元素
__Tip:__ 此时的行为和js相同。
~~~ xml
  <block wx:for="{{obj.data.name}}"><view>{{item}}</view></block>
~~~
当obj不存在的时候，即：obj===undefined。不管引用了多少层。wx:if什么都不做，并不出错。

__Tip:__ 此时的行为和js不同，不过极可能正是你想要的效果。
