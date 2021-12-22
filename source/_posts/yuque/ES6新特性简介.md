---
title: ES6新特性简介
urlname: fbpn35
date: '2021-12-22 13:16:28 +0800'
tags: Js
categories: Js
---

## 一、新的变量声明方式 let/const

**let** 声明的变量只在 let 命令所在的代码块 （即 JavaScript 块级作用域)内有效

```javascript
{
  let _a = 20;
}
console.log(a); // a is not defined
```

**const**用于声明一个或多个常量，声明同时须进行初始化，一旦声明，常量值则不能再修改。但是使用 const 定义的常量对象或者常量数组是可变的，可对其对象或属性进行添加和修改等操作，但不能对其进行重新赋值。

```javascript
const obDev = {
  a: 20,
  b: 30,
};
obDev.a = 30;
console.log(obDev); // Object {a: 30, b: 30}
```

## 二、 箭头函数的使用

```javascript
// es5
var fn = function(a, b) {
    return a + b;
}

// es6 箭头函数写法，当函数直接被return时，可以省略函数体的括号
const fn = (a, b) => a + b;

// es5
var foo = function() {
    var a = 20；
    var b = 30;
    return a + b;
}

// es6
const foo = () => {
   const a = 20;
   const b = 30;
   return a + b;
}
```

其次还有一个至关重要的一点，那就是箭头函数中，没有 this。如果你在箭头函数中使用了 this，那么该 this 一定就是外层的 this。

```javascript
var person = {
  name: "tom",
  getName: function () {
    return this.name;
  },
};

// 用ES6的写法来重构上面的对象
const person = {
  name: "tom",
  getName: () => this.name,
};

// 但是编译结果却是
var person = {
  name: "tom",
  getName: function getName() {
    return undefined.name;
  },
};
//在ES6中，会默认采用严格模式，因此this也不会自动指向window对象了，而箭头函数本身并没有this，因此this就只能是undefined,可以使用es5中常用的定义变量来保存this
```

## 三、模板字符串

使用 ``(反引号) 将整个字符串包裹起来，而在其中使用 ${} 来包裹一个变量或者一个表达式。

```javascript
// es6
const a = 20;
const b = 30;
const string = `${a}+${b}=${a + b}`; // "20+30=50"

// es5
var a = 20;
var b = 30;
var string = a + "+" + b + "=" + (a + b); // "20+30=50"
```

## 四、 解析结构

```javascript
// 首先有这么一个对象
const props = {
  loading: false,
  clicked: true,
};
// 当我们想要取得其中的2个值：loading与clicked时：

// es5
var loading = props.loading;
var clicked = props.clicked;

// es6
const { loading, clicked } = props;

// 给一个默认值，当props对象中找不到loading时，loading就等于该默认值
const { loading = false, clicked } = props;

// 项目中使用场景
// section1
import Vue from "Vue";

// section2
export { default } from "./Button";

// section3
const { click, loading } = this.props;
const { isCheck } = this.state;
```

## 五、 函数默认参数

```javascript
//es5
function add(x, y) {
  var x = x || 20;
  var y = y || 30;
  return x + y;
}

console.log(add()); // 50

//es6
function add(x = 20, y = 30) {
  return x + y;
}

console.log(add()); //50
```

## 六、 展开运算符

在 ES6 中用...来表示展开运算符，它可以将数组方法或者对象进行展开

```javascript
1.数组中的使用

// 数组的合并
var a = [1, 2];
var b = [0, ...a, 3]
console.log(b) // [0, 1, 2, 3]

// 数组的分割

var [a, ...b] = [0, 1, 2];
console.log(b) // [1, 2]

// 数组的拷贝

var a = [1, 2];
var b = [...a];
console.log(b) // [1, 2]

2.对象中的使用(指{a: 1}这种对象,)

let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
console.log(z) // {a: 3, b: 4}

3.解析结果中使用
// 当不确定props到底还有多少数据会传进来，就可以利用展开运算符来处理剩余的数据
const props = {
  size: 1,
  src: 'xxxx',
  mode: 'si'
}
const { size, ...others } = props;

console.log(others)//{src: "xxxx", mode: "si"}
```

## 七、对象字面量

```javascript
1.当属性与值的变量同名时

const name = 'Jane';
const age = 20

// es6
const person = {
  name,
  age
}

// es5
var person = {
  name: name,
  age: age
};

2.当一个模块对外提供接口时
const getName = () => person.name;
const getAge = () => person.age;

// ES6 modules的方式
export default { getName, getAge  }

3.在对象字面量中可以使用中括号作为属性，表示属性名也能是一个变量
const name = 'Jane';
const age = 20

const person = {
  [name]: true,
  [age]: true
}
console.log(person) // {20: true, Jane: true}
```

## 八、Promise

1、主要用于异步计算
2、可以将异步操作队列化，按照期望的顺序执行，返回符合预期的结果
3、可以在对象之间传递和操作 promise，帮助我们处理队列

异步回调的问题：

```
 1. 很容易多层嵌套，剥夺了函数return的能力
 2. 问题可以解决，但是难以读懂，
 3. 维护困难 嵌套层次深，不好维护
```

```javascript
$.ajax({
  url: "https://www.mock.com/city",
  success(resCity) {
    let findCityId = resCity.filter((item) => {
      if (item.id === "c1") {
        return item;
      }
    })[0].id;
    $.ajax({
      // 第二个api,根据上一个返回的 findCityId
      url: "https://www.mock.com/position-list",
      success(resPosition) {
        let findPostion = resPosition.filter((item) => {
          if (item.cityId === findCityId) {
            return item;
          }
        })[0].id;
      },
    });
  },
});
```

Promise

```
 1. promise是一个对象，对象和函数的区别就是对象可以保存状态，函数不可以（闭包除外）
 2. 并未剥夺函数return的能力，因此无需层层传递callback，进行回调获取数据
 3. 容易理解，便于维护
 4. 多个异步等待合并便于解决
```

- resolve 作用是，将 Promise 对象的状态从“未完成”变为“成功”（即从 pending 变为           resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
  reject 作用是，将 Promise 对象的状态从“未完成”变为“失败”（即从 pending 变为
  rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。
- promise 有三个状态：
  1、pending[待定]初始状态
  2、fulfilled[实现]操作成功
  3、rejected[被否决]操作失败
  当 promise 状态发生改变，就会触发 then()里的响应函数处理后续步骤；
- promise 状态一经改变，不会再变。
  Promise 对象的状态改变，只有两种可能：
  从 pending 变为 fulfilled
  从 pending 变为 rejected。                                                             这两种情况只要发生，状态就凝固了，不会再变了。

```javascript
//  分两次，顺序执行
new Promise((resolve) => {
  setTimeout(() => {
    resolve("hello");
  }, 2000);
})
  .then((val) => {
    console.log(val); //  参数val = 'hello'
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve("world");
      }, 2000);
    });
  })
  .then((val) => {
    console.log(val); // 参数val = 'world'
  });
```

错误处理
1.throw new Error('错误信息').catch( () => {错误处理逻辑})

```javascript
   new Promise((resolve) => {
       setTimeout( ()=> {
           resolve()
       }, 2000)
   }).then((val)=>{
       console.log('start')
       throw new Error('test error')
   }).catch(error =>{
       console.log('I catch', error)
   }).then(()=>{
       console.log('arrive here')
   }).then(()=>{
       console.log('... and here')
   }).catch(error =>{
       console.log('No, I catch', error)
   })
   // 打印结果
   start
   I catch Error: test error
   arrive here
   ... and here
```

2.reject('错误信息').then(() => {}, () => {错误处理逻辑})

```javascript
new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("bye");
  }, 2000);
}).then(
  (val) => {
    console.log(val);
  },
  (err) => {
    console.log("error:", err); //  error:bye
  }
);
```

Promise.all() 批量执行
Promise.all([p1, p2,p3])用于将多个 promise 实例，包装成一个新的 Promise 实例，返回的实例就是普通的 promise
它接收一个数组作为参数
数组里可以是 Promise 对象，也可以是别的值，只有 Promise 会等待状态改变
当所有的子 Promise 都完成，该 Promise 完成，返回值是全部值得数组
有任何一个失败，该 Promise 失败，返回值是第一个失败的子 Promise 结果

```javascript
//切菜
  function cutUp(){
      console.log('开始切菜。');
      var p = new Promise(function(resolve, reject){        //做一些异步操作
          setTimeout(function(){
              console.log('切菜完毕！');
              resolve('切好的菜');
          }, 1000);
      });
      return p;
  }

  //烧水
  function boil(){
      console.log('开始烧水。');
      var p = new Promise(function(resolve, reject){        //做一些异步操作
          setTimeout(function(){
              console.log('烧水完毕！');
              resolve('烧好的水');
          }, 1000);
      });
      return p;
  }
  Promise.all([cutUp(), boil()])
      .then((result) => {
          console.log('准备工作完毕');
          console.log(result);
      })

//返回结果
开始切菜。
开始烧水。
切菜完毕！
烧水完毕！
准备工作完毕
["切好的菜", "烧好的水"]
```

Promise.race() 类似于 Promise.all() ，区别在于它有任意一个完成就算完成

```javascript
let p1 = new Promise((resolve) => {
  setTimeout(() => {
    resolve("I`m p1 ");
  }, 1000);
});
let p2 = new Promise((resolve) => {
  setTimeout(() => {
    resolve("I`m p2 ");
  }, 2000);
});
Promise.race([p1, p2]).then((value) => {
  console.log(value);
});

// I`m p1
```
