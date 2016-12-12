# wx-mp-doc
## 数据绑定

### 字符串运算
~~~ xml
  <view class="price">{{info.name + info.family.addr}}</view>
~~~

* 如果info是空，不会报错，生成的结果和JS相同。undefined + undefined = NaN
* 如果family是空，不会报错，生成的结果和JS相同。string + undefined = stringundefined
* 如果属性都有效，正常的加法运算

之所以这样的原因是因为微信的语法解析分三部分，优先级高的点引用在先，加法在后。是这样的

~~~
    var scr = "info.name + info.family.addr".split(' ')
    var oper = scr[1];

    "info.name".split('.').find in dataMap
    "info.family.addr".split('.') find in dataMap
    if(null) {
        return undefined
    }
    //注意：想想oper是undefined会怎样？
    eval(a oper b) || ''

~~~

目前支持的operation有所有算术运算、位运算和逻辑运算，三目运算。所以就不难理解下面的写法将会引发致命错误


~~~ xml
    <view class="price">{{info.name info.family.addr}}</view>
~~~

因为找不到operation，View Thread抛出错误。渲染失败，页面呲掉。

~~~ xml
    <view class="price">{{info.name}}{{info.family.addr}}</view>
~~~

这是最安全的写法，不过很难看懂。它相当于输出（渲染）

~~~ js
console.log(a||''); console.log(b||'');
~~~

然而下面的，哈哈，它就不是一个运算了，
~~~ xml
    <view class="price">{{info.name}} + {{info.family.addr}}</view>
~~~
而是输出（渲染）
~~~ js
    console.log(a||''); console.log('+'); console.log(b||'');
~~~


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

* 当obj不存在的时候，即：`obj = undefined`。不管引用了多少层。wx:if什么都不做，并不出错。

__Tip:__ 此时的行为和js不同，不过极可能正是你想要的效果。

* 但是，尤其要注意。当`obj = null`的时候，上面的代码会抛出异常。
原因是，微信小程序渲染线程中，把null当成object。
当尝试访问null的某个属性（这里为val）时，发生了异常。
同样，在wx:for中也会这样。微信小程序渲染线程的本来目的是要容错，
那么起码在版本v0.11.112301之前，可以说这里的情况是其自身的一个bug。

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
当obj不存在的时候，即：`obj=undefined`。不管引用了多少层。wx:if什么都不做，并不出错。

__Tip:__ 此时的行为和js不同，不过极可能正是你想要的效果。

* 尤其要注意。当`obj = null`时，页面会异常。

### <block wx:for> 和 <view wx:for>的区别

|\<block wx:for\>|\<view wx:for\>|
|---|----
|aaa|bbb
|aaa|ccc



