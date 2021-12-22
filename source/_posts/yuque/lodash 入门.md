---
title: lodash 入门
urlname: obk3qh
date: '2021-12-22 13:28:17 +0800'
tags: lodash
categories: lodash
---

## 简介

Lodash 是一个著名的 javascript 原生库，不需要引入其他第三方依赖。是一个意在提高开发者效率,提高 JS 原生方法性能的 JS 库。简单的说就是，很多方法 lodash 已经帮你写好了，直接调用就行，不用自己费尽心思去写了，而且可以统一方法的一致性。Lodash 使用了一个简单的 \_ 符号，就像 Jquery 的 $ 一样，十分简洁。
类似的还有 Underscore.js 和 Lazy.js

## 支持

chrome 43 往上
Firefox 38 往上
IE 6-11
MS Edge
Safari 5 往上
（几乎涵盖现在市面上可以见到的大部分浏览器）

## 如何安装

浏览器
NPM
$ npm i -g npm$ npm i --save lodash 复制代码
先全局安装，在单独安装到项目中
node.js
var \_ = require('lodash')

## 为什么使用 lodash

通过使用数组，数字，对象，字符串等方法，Lodash 使 JavaScript 变得更简单。

## 模块组成

Lodash 听得辅助函数主要分为以下几类，函数列表和用法实力请查看 Lodash 的官方文档：

Array， 适合于数组类型，比如填充数据、查找元素、数组分片等操作

Collocation， 适用于数组和对象类型，部分适用于字符串，比如分组、查找、过滤等操作

Function， 适用于函数类型，比如节流、延迟、缓存、设置钩子等操作

Lang， 普遍适用于各种类型，常用于执行类型判断和类型转换

Math， 使用与数值类型，常用于执行数学运算

Number， 适用于生成随机数，比较数值与数值区间的关系

Object， 适用于对象类型，常用于对象的创建、扩展、类型转换、检索、集合等操作

Seq， 常用于创建链式调用，提高执行性能（惰性计算）

String， 适用于字符串类型

lodash/fp 模块提供了更接近函数式编程的开发方法，其内部的函数经过包装，具有 immutable、auto-curried、iteratee-first、data-last（官方介绍）等特点。

Fixed Arity，固化参数个数，便于柯里化

Rearragned Arguments， 重新调整参数位置，便于函数之间的聚合

Capped Iteratee Argument， 封装 Iteratee 参数

## 1、N 次循环

```
<script type="text/javascript">
 console.log('------- javascript -------')
 //js原生的循环方法
 for(var i = 0; i < 5; i++){
  console.log(i);
 }
 console.log('------- lodash -------')
 //ladash的times方法
 _.times(5,function(a){
  console.log(a)
 });</script>
```

for 语句是执行循环的不二选择，但在上面代码的使用场景下，\_.times()的解决方式更加简洁和易于理解。

## 2、深层查找属性值

```
<script type="text/javascript">
    var ownerArr = [{
        "owner": "Colin",
        "pets": [{"name": "dog1"}, {"name": "dog2"}]
    }, {
        "owner": "John",
        "pets": [{"name": "dog3"}, {"name": "dog4"}]
    }];
    var jsMap = ownerArr.map(function (owner) {
        return owner.pets[0].name;
    });
    console.log('------- jsMap -------');
    console.log(jsMap);

    var lodashMap = _.map(ownerArr, 'pets[0].name');
    console.log('------- lodashMap -------');
    console.log(lodashMap);</script>
```

Lodash 中的\_.map 方法和 JavaScript 中原生的数组方法非常的像，但它还是有非常有用的升级。 你可以通过一个字符串而不是回调函数来浏览深度嵌套的对象属性。

## 3、深克隆对象

```
<script type="text/javascript">
    var objA = {
        "name": "戈德斯文"
    };
    var objB = _.cloneDeep(objA);
    console.log(objA);
    console.log(objB);
    console.log(objA === objB);</script>
```

深度克隆 JavaScript 对象是困难的，并且也没有什么简单的解决方案。你可以使用原生的解决方案:JSON.parse(JSON.stringify(objectToClone))  进行深度克隆。但是，这种方案仅在对象内部没有方法的时候才可行。

## 4、在指定范围内获取一个随机值

```
<script type="text/javascript">
    function getRandomNumber(min, max){
        return Math.floor(Math.random() * (max - min)) + min;
    }
    console.log(getRandomNumber(15, 20));

    console.log(_.random(15, 20));
</script>
```

Lodash 中的  *.random  方法要比上面的原生方法更强大与灵活。你可以只传入一个参数作为最大值， 你也可以指定返回的结果为浮点数*.random(15,20,true)

## 5、扩展对象

```
<script type="text/javascript">
    Object.prototype.extend = function(obj) {
        for (var i in obj) {
            if (obj.hasOwnProperty(i)) {    //判断被扩展的对象有没有某个属性，
                this[i] = obj[i];
            }
        }
    };

    var objA = {"name": "戈德斯文", "car": "宝马"};
    var objB = {"name": "柴硕", "loveEat": true};

    objA.extend(objB);
    console.log(objA);

    console.log(_.assign(objA, objB));</script>
```

\_.assign  方法也可以接收多个参数对象进行扩展，都是往后面的对象上合并

## 6、从列表中随机的选择列表项

```
<script type="text/javascript">
    var smartTeam = ["戈德斯文", "杨海月", "柴硕", "师贝贝"];

    function randomSmarter(smartTeam){
        var index = Math.floor(Math.random() * smartTeam.length);
        return smartTeam[index];
    }

    console.log(randomSmarter(smartTeam));

    // Lodash
    console.log(_.sample(smartTeam));
    console.log(_.sampleSize(smartTeam,2));</script>
```

此外，你也可以指定随机返回元素的个数\_.sampleSize(smartTeam,n)，n 为需要返回的元素个数

## 7、判断对象中是否含有某元素

```
<script type="text/javascript">
    var smartPerson = {
            'name': '戈德斯文',
            'gender': 'male'
        },
        smartTeam = ["戈德斯文", "杨海月", "柴硕", "师贝贝"];


    console.log(_.includes(smartPerson, '戈德斯文'));
    console.log(_.includes(smartTeam, '杨海月'));
    console.log(_.includes(smartTeam, '杨海月',2));</script>
```

\_.includes()第一个参数是需要查询的对象，第二个参数是需要查询的元素，第三个参数是开始查询的下标

## 8、遍历循环

```
<script type="text/javascript">
    _([1, 2]).forEach(function(value) {
        console.log(value);
    });
    _.forEach([1, 3] , function(value, key) {
        console.log(key,value);
    });</script>
```

这两种方法都会分别输出‘1’和‘2’，不仅是数组，对象也可以，数组的是后 key 是元素的下标，当传入的是对象的时候，key 是属性，value 是值

## 9、遍历循环执行某个方法

\_.map()

```
<script type="text/javascript">
    function square(n) {
        return n * n;
    }

    console.log(_.map([4, 8], square));
    // => [16, 64]

    console.log(_.map({ 'a': 4, 'b': 8 }, square));
    // => [16, 64] (iteration order is not guaranteed)

    var users = [
        { 'user': 'barney' },
        { 'user': 'fred' }
    ];

    // The `_.property` iteratee shorthand.
    console.log(_.map(users, 'user'));
    // => ['barney', 'fred']</script>
```

## 10、检验值是否为空

\_.isEmpty()

```
<script type="text/javascript">
    _.isEmpty(null);
    // => true

    _.isEmpty(true);
    // => true

    _.isEmpty(1);
    // => true

    _.isEmpty([1, 2, 3]);
    // => false

    _.isEmpty({ 'a': 1 });
    // => false</script>
```

## 11、查找属性

_.find()、_.filter()、\_.reject()

```javascript
<script type="text/javascript">
    var users = [
        {'user': 'barney', 'age': 36, 'active': true},
        {'user': 'fred', 'age': 40, 'active': false},
        {'user': 'pebbles', 'age': 1, 'active': true}
    ];

    console.log(_.find(users, function (o) {
        return o.age < 40;
    }));
    console.log(_.find(users, {'age': 1, 'active': true}));
    console.log(_.filter(users, {'age': 1, 'active': true}));
    console.log(_.find(users, ['active', false]));
    console.log(_.filter(users, ['active', false]));
    console.log(_.find(users, 'active'));
    console.log(_.filter(users, 'active'));
</script>
```

_.find()第一个返回真值的第一个元素。_.filter()返回真值的所有元素的数组。 *.reject()是*.filter 的反向方法，不返回真值的（集合）元素

## 12、数组去重

\_.uniq(array)创建一个去重后的 array 数组副本。参数  array (Array): 要检查的数组。
返回新的去重后的数组

```javascript
<script type="text/javascript">
    var arr1 = [2, 1, 2];

    var arr2 = _.uniq(arr1);


    function unique(arr) {
        var newArr = [];
        for (var i = 0; i < arr.length; i++) {
            if(newArr.indexOf(arr[i]) == -1){
                newArr.push(arr[i]);
            }
        }
        return newArr;
    }

    console.log(arr1);
    console.log(arr2);
    console.log(unique(arr1));</script>
```

_.uniqBy(array,[iteratee=_.identity])这个方法类似  \_.uniq，除了它接受一个  iteratee（迭代函数），调用每一个数组（array）的每个元素以产生唯一性计算的标准。iteratee 调用时会传入一个参数：(value)。

```
<script type="text/javascript">
    console.log(_.uniqBy([2.1, 1.2, 2.3], Math.floor));
    // => [2.1, 1.2]

    console.log(_.uniqBy([{ 'x': 1 }, { 'x': 2 }, { 'x': 1 }], 'x'));
    // => [{ 'x': 1 }, { 'x': 2 }]</script>
```

Math.floor 只是向下取整，去重，并没有改变原有的数组，所以还是 2.1 和 1.2，不是 2 和 1。

## 13、模板插入

\_.template([string=''], [options={}])

```
<div id="container"></div>
<script src="https://cdn.bootcss.com/lodash.js/4.17.4/lodash.min.js"></script><script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script><script type="text/javascript">
    $(function () {
        var data = [{name: '戈德斯文'}, {name: '柴硕'}, {name: '杨海月'}];
        $('#container').html( _.template($('#tpl').html(), data) )
    });</script><script type="text/template" id="tpl">
    <% _.each(obj,function(e,i){ %>
        <ul>
            <li><%= e.name %><%= i %></li>
        </ul>
    <%})%></script>
 注意，这个<script>标签的type是text/template，类似于react的JSX的写法，就是js和html可以混写，用<% %>括起来的就是js代码，可以执行，直接写的就是html的标签，并且有类似MVC框架的的数据绑定，在<%= %>中可以调用到数据呈现（纯属个人见解，不知道理解的对不对）
```
