---
title: Event Loop 事件循环
urlname: ggxybc
date: '2021-12-22 13:25:48 +0800'
tags: Js
categories: Js
---

# Event Loop

## 单线程

JavaScript 语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。那么，为什么 JavaScript 不能有多个线程呢？这样能提高效率啊。

JavaScript 的单线程，与它的用途有关。作为浏览器脚本语言，JavaScript 的主要用途是与用户互动，以及操作 DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定 JavaScript 同时有两个线程，一个线程在某个 DOM 节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

所以，为了避免复杂性，从一诞生，JavaScript 就是单线程，这已经成了这门语言的核心特征，将来也不会改变。

为了利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。所以，这个新标准并没有改变 JavaScript 单线程的本质。

## 任务队列

单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。

如果排队是因为计算量大，CPU 忙不过来，倒也算了，但是很多时候 CPU 是闲着的，因为 IO 设备（输入输出设备）很慢（比如 Ajax 操作从网络读取数据），不得不等着结果出来，再往下执行。

JavaScript 语言的设计者意识到，这时主线程完全可以不管 IO 设备，挂起处于等待中的任务，先运行排在后面的任务。等到 IO 设备返回了结果，再回过头，把挂起的任务继续执行下去。

于是，所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

具体来说，异步执行的运行机制如下。（同步执行也是如此，因为它可以被视为没有异步任务的异步执行。）

> （1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。

（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

（4）主线程不断重复上面的第三步。

## 事件和回调函数

"任务队列"是一个事件的队列（也可以理解成消息的队列），IO 设备完成一项任务，就在"任务队列"中添加一个事件，表示相关的异步任务可以进入"执行栈"了。主线程读取"任务队列"，就是读取里面有哪些事件。

"任务队列"中的事件，除了 IO 设备的事件以外，还包括一些用户产生的事件（比如鼠标点击、页面滚动等等）。只要指定过回调函数，这些事件发生时就会进入"任务队列"，等待主线程读取。

所谓"回调函数"（callback），就是那些会被主线程挂起来的代码。异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

"任务队列"是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。主线程的读取过程基本上是自动的，只要执行栈一清空，"任务队列"上第一位的事件就自动进入主线程。但是，由于存在后文提到的"定时器"功能，主线程首先要检查一下执行时间，某些事件只有到了规定的时间，才能返回主线程。

## Event Loop 是什么

**event loop 是一个执行模型，在不同的地方有不同的实现。浏览器和 NodeJS 基于不同的技术实现了各自的 Event Loop。**

- 浏览器的 Event Loop 是在[html5 的规范](https://www.w3.org/TR/html5/webappapis.html#event-loops)中明确定义。
- NodeJS 的 Event Loop 是基于 libuv 实现的。可以参考 Node 的[官方文档](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)以及 libuv 的[官方文档](http://docs.libuv.org/en/v1.x/design.html)。
- libuv 已经对 Event Loop 做出了实现，而 HTML5 规范中只是定义了浏览器中 Event Loop 的模型，具体的实现留给了浏览器厂商。

## 宏队列和微队列

**宏队列，macrotask，也叫 tasks。** 一些异步任务的回调会依次进入 macro task queue，等待后续被调用，这些异步任务包括：

- setTimeout
- setInterval
- setImmediate (Node 独有)
- requestAnimationFrame (浏览器独有)
- I/O
- UI rendering (浏览器独有)

**微队列，microtask，也叫 jobs**。 另一些异步任务的回调会依次进入 micro task queue，等待后续被调用，这些异步任务包括：

- process.nextTick (Node 独有)
- Promise
- Object.observe
- MutationObserver

（注：这里只针对浏览器和 NodeJS）

## 浏览器的 Event Loop

我们先来看一张图，再看完这篇文章后，请返回来再仔细看一下这张图，相信你会有更深的理解。

![2021-03-24_Event Loop_1.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1640150802231-e69d822f-b5db-4e70-bda3-bb3b068cf0b2.png#clientId=u3788e183-4257-4&from=ui&id=u78e0afde&margin=%5Bobject%20Object%5D&name=2021-03-24_Event%20Loop_1.png&originHeight=732&originWidth=694&originalType=binary∶=1&size=48117&status=done&style=none&taskId=ue9a67d43-2d75-4de6-a370-a14f7caa447)

这张图将浏览器的 Event Loop 完整的描述了出来，我来讲执行一个 JavaScript 代码的具体流程：

1. 执行全局 Script 同步代码，这些同步代码有一些是同步语句，有一些是异步语句（比如 setTimeout 等）；
1. 全局 Script 代码执行完毕后，调用栈 Stack 会清空；
1. 从微队列 microtask queue 中取出位于队首的回调任务，放入调用栈 Stack 中执行，执行完后 microtask queue 长度减 1；
1. 继续取出位于队首的任务，放入调用栈 Stack 中执行，以此类推，直到直到把 microtask queue 中的所有任务都执行完毕。**注意，如果在执行 microtask 的过程中，又产生了 microtask，那么会加入到队列的末尾，也会在这个周期被调用执行；**
1. microtask queue 中的所有任务都执行完毕，此时 microtask queue 为空队列，调用栈 Stack 也为空；
1. 取出宏队列 macrotask queue 中位于队首的任务，放入 Stack 中执行；
1. 执行完毕后，调用栈 Stack 为空；
1. 重复第 3-7 个步骤；
1. 重复第 3-7 个步骤；
1. ...

**可以看到，这就是浏览器的事件循环 Event Loop**

这里归纳 3 个重点：

1. 宏队列 macrotask 一次只从队列中取一个任务执行，执行完后就去执行微任务队列中的任务；
1. 微任务队列中所有的任务都会被依次取出来执行，知道 microtask queue 为空；
1. 图中没有画 UI rendering 的节点，因为这个是由浏览器自行判断决定的，但是只要执行 UI rendering，它的节点是在执行完所有的 microtask 之后，下一个 macrotask 之前，紧跟着执行 UI render。

好了，概念性的东西就这么多，来看几个示例代码，测试一下你是否掌握了:

```javascript
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3);
  });
});

new Promise((resolve, reject) => {
  console.log(4);
  resolve(5);
}).then((data) => {
  console.log(data);
});

setTimeout(() => {
  console.log(6);
});

console.log(7);
```

这里结果会是什么呢？运用上面了解到的知识，先自己做一下试试看。

```html
// 正确答案 1 4 7 5 2 3 6
```

你答对了吗？

我们来分析一下整个流程：

- 执行全局 Script 代码

### Step 1

```javascript
console.log(1);
```

Stack Queue: [console]

Macrotask Queue: []

Microtask Queue: []

> 打印结果：
> 1

### Step 2

```javascript
setTimeout(() => {
  // 这个回调函数叫做callback1，setTimeout属于macrotask，所以放到macrotask queue中
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3);
  });
});
```

Stack Queue: [setTimeout]

Macrotask Queue: [callback1]

Microtask Queue: []

> 打印结果：
> 1

### Step 3

```javascript
new Promise((resolve, reject) => {
  // 注意，这里是同步执行的
  console.log(4);
  resolve(5);
}).then((data) => {
  // 这个回调函数叫做callback2，promise属于microtask，所以放到microtask queue中
  console.log(data);
});
```

Stack Queue: [promise]

Macrotask Queue: [callback1]

Microtask Queue: [callback2]

> 打印结果：
> 1
> 4

### Step 4

```javascript
setTimeout(() => {
  // 这个回调函数叫做callback3，setTimeout属于macrotask，所以放到macrotask queue中
  console.log(6);
});
```

Stack Queue: [setTimeout]

Macrotask Queue: [callback1, callback3]

Microtask Queue: [callback2]

> 打印结果：
> 1
> 4

### Step 5

```javascript
console.log(7);
```

Stack Queue: [console]

Macrotask Queue: [callback1, callback3]

Microtask Queue: [callback2]

> 打印结果：
> 1
> 4
> 7

- 全局 Script 代码执行完了，进入下一个步骤，从 microtask queue 中依次取出任务执行，直到 microtask queue 队列为空。

### Step 6

```javascript
console.log(data); // 这里data是Promise的决议值5
```

Stack Queue: [callback2]

Macrotask Queue: [callback1, callback3]

Microtask Queue: []

> 打印结果：
> 1
> 4
> 7
> 5

- 这里 microtask queue 中只有一个任务，执行完后开始从宏任务队列 macrotask queue 中取位于队首的任务执行

### Step 7

```javascript
console.log(2);
```

Stack Queue: [callback1]

Macrotask Queue: [callback3]

Microtask Queue: []

> 打印结果：
> 1
> 4
> 7
> 5
> 2

但是，执行 callback1 的时候又遇到了另一个 Promise，Promise 异步执行完后在 microtask queue 中又注册了一个 callback4 回调函数

### Step 8

```javascript
Promise.resolve().then(() => {
  // 这个回调函数叫做callback4，promise属于microtask，所以放到microtask queue中
  console.log(3);
});
```

Stack Queue: [promise]

Macrotask v: [callback3]

Microtask Queue: [callback4]

> 打印结果：
> 1
> 4
> 7
> 5
> 2

- 取出一个宏任务 macrotask 执行完毕，然后再去微任务队列 microtask queue 中依次取出执行

### Step 9

```javascript
console.log(3);
```

Stack Queue: [callback4]

Macrotask Queue: [callback3]

Microtask Queue: []

> 打印结果：
> 1
> 4
> 7
> 5
> 2  
> 3

- 微任务队列全部执行完，再去宏任务队列中取第一个任务执行

### Step 10

```javascript
console.log(6);
```

Stack Queue: [callback3]

Macrotask Queue: []

Microtask Queue: []

> 打印结果：
> 1
> 4
> 7
> 5
> 2
> 3
> 6

- 以上，全部执行完后，Stack Queue 为空，Macrotask Queue 为空，Micro Queue 为空

Stack Queue: []

Macrotask Queue: []

Microtask Queue: []

因为是第一个例子，所以这里分析的比较详细，大家仔细看一下，接下来我们再来一个例子：

```javascript
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3);
  });
});

new Promise((resolve, reject) => {
  console.log(4);
  resolve(5);
}).then((data) => {
  console.log(data);

  Promise.resolve()
    .then(() => {
      console.log(6);
    })
    .then(() => {
      console.log(7);

      setTimeout(() => {
        console.log(8);
      }, 0);
    });
});

setTimeout(() => {
  console.log(9);
});

console.log(10);
```

最终输出结果是什么呢？参考前面的例子，好好想一想......

```html
// 正确答案 1 4 10 5 6 7 2 3 9 8
```

相信大家都答对了，这里的关键在前面已经提过：

**在执行微队列 microtask queue 中任务的时候，如果又产生了 microtask，那么会继续添加到队列的末尾，也会在这个周期执行，直到 microtask queue 为空停止。**

注：当然如果你在 microtask 中不断的产生 microtask，那么其他宏任务 macrotask 就无法执行了，但是这个操作也不是无限的，拿 NodeJS 中的微任务 process.nextTick()来说，它的上限是 1000 个，后面我们会讲到。

浏览器的 Event Loop 就说到这里，下面我们看一下 NodeJS 中的 Event Loop，它更复杂一些，机制也不太一样。

## NodeJS 中的 Event Loop

### libuv

先来看一张 libuv 的结构图：

![2021-03-24_Event Loop_2.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1640150869182-c542d656-f43b-4b7b-ab76-07395f20db36.png#clientId=u3788e183-4257-4&from=ui&id=uc3ec049c&margin=%5Bobject%20Object%5D&name=2021-03-24_Event%20Loop_2.png&originHeight=289&originWidth=732&originalType=binary∶=1&size=141080&status=done&style=none&taskId=uc5809e64-e5f8-47c8-bed4-c2612d7a9af)

### NodeJS 中的宏队列和微队列

NodeJS 的 Event Loop 中，执行宏队列的回调任务有**6 个阶段**，如下图：
![2021-03-24_Event Loop_3.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1640150881821-daf37c3f-7f75-4b45-a655-133cf582187e.png#clientId=u3788e183-4257-4&from=ui&id=u712b2992&margin=%5Bobject%20Object%5D&name=2021-03-24_Event%20Loop_3.png&originHeight=339&originWidth=670&originalType=binary∶=1&size=8419&status=done&style=none&taskId=u9be5125f-53de-487d-84c8-cf78bb786fa)

各个阶段执行的任务如下：

- **timers 阶段**：这个阶段执行 setTimeout 和 setInterval 预定的 callback
- **I/O callback 阶段**：执行除了 close 事件的 callbacks、被 timers 设定的 callbacks、setImmediate()设定的 callbacks 这些之外的 callbacks
- **idle, prepare 阶段**：仅 node 内部使用
- **poll 阶段**：获取新的 I/O 事件，适当的条件下 node 将阻塞在这里
- **check 阶段**：执行 setImmediate()设定的 callbacks
- **close callbacks 阶段**：执行 socket.on('close', ....)这些 callbacks

#### NodeJS 中宏队列主要有 4 个

由上面的介绍可以看到，回调事件主要位于 4 个 macrotask queue 中：

1. Timers Queue
1. IO Callbacks Queue
1. Check Queue
1. Close Callbacks Queue

这 4 个都属于宏队列，但是在浏览器中，可以认为只有一个宏队列，所有的 macrotask 都会被加到这一个宏队列中，但是在 NodeJS 中，不同的 macrotask 会被放置在不同的宏队列中。

#### NodeJS 中微队列主要有 2 个

1. Next Tick Queue：是放置 process.nextTick(callback)的回调任务的
1. Other Micro Queue：放置其他 microtask，比如 Promise 等

在浏览器中，也可以认为只有一个微队列，所有的 microtask 都会被加到这一个微队列中，但是在 NodeJS 中，不同的 microtask 会被放置在不同的微队列中。

具体可以通过下图加深一下理解：

![2021-03-24_Event Loop_4.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1640150892410-a170dd7d-c70b-42b6-bf90-5ac6f1a9fce8.png#clientId=u3788e183-4257-4&from=ui&id=u32cf3abc&margin=%5Bobject%20Object%5D&name=2021-03-24_Event%20Loop_4.png&originHeight=405&originWidth=732&originalType=binary∶=1&size=54689&status=done&style=none&taskId=u05d620c9-3659-46ae-b493-fe98d5ca38a)

大体解释一下 NodeJS 的 Event Loop 过程：

1. 执行全局 Script 的同步代码
1. 执行 microtask 微任务，先执行所有 Next Tick Queue 中的所有任务，再执行 Other Microtask Queue 中的所有任务
1. 开始执行 macrotask 宏任务，共 6 个阶段，从第 1 个阶段开始执行相应每一个阶段 macrotask 中的所有任务，注意，这里是所有每个阶段宏任务队列的所有任务，在浏览器的 Event Loop 中是只取宏队列的第一个任务出来执行，每一个阶段的 macrotask 任务执行完毕后，开始执行微任务，也就是步骤 2
1. Timers Queue -> 步骤 2 -> I/O Queue -> 步骤 2 -> Check Queue -> 步骤 2 -> Close Callback Queue -> 步骤 2 -> Timers Queue ......
1. 这就是 Node 的 Event Loop
