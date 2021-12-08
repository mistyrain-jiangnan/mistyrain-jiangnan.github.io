---
title: 节流和防抖
urlname: adw9ye
date: '2021-08-06 16:48:56 +0800'
tags: 函数
categories: 函数
---

## 防抖

debounce：当持续触发事件时，一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定的时间到来之前，又一次触发了事件，就重新开始延时
如下图，持续触发 scroll 事件时，并不执行 handle 函数，当 1000ms 内没有触发 scroll 事件时，才会延时触发 scroll 事件
![](https://cdn.nlark.com/yuque/0/2021/png/12838787/1628242113416-3f6c7ede-c78b-44c0-8782-053b678ef9f0.png#align=left&display=inline&height=148&margin=%5Bobject%20Object%5D&originHeight=148&originWidth=698&size=0&status=done&style=none&width=698)

```javascript
function debounce(fn, wait) {
  let timeout = null;
  return function () {
    if (timeout !== null) {
      clearTimeout(timeout);
    }
    timeout = setTimeout(fn, wait);
  };
}
// 处理函数
function handle() {
  console.log("处理函数", Math.random());
}
// 滚动事件
vue中在monuted中监听滚动;
window.addEventListener("scroll", debounce(handle, 1000));
```

## 节流

throttle：当持续触发事件时，保证一定时间段内只调用一次事件处理函数
仔细了解了才知道，我以前刚学前端的时候，做 banner 图特效，两边的点击按钮如果一直重复点击就会出问题，后面摸索了此方法，原来这名字叫做节流
如下图，持续触发 scroll 事件时，并不立即执行 handle 函数，每隔 1000 毫秒才会执行一次 handle 函数
![](https://cdn.nlark.com/yuque/0/2021/png/12838787/1628242200176-77c64e04-5c8a-4653-bba6-ec40b2a50769.png#align=left&display=inline&height=319&margin=%5Bobject%20Object%5D&originHeight=319&originWidth=328&size=0&status=done&style=none&width=328)
**时间戳方法**

```javascript
let throttle = function (func, delay) {
  let prev = Date.now();
  return function () {
    let context = this;
    let args = arguments;
    let now = Date.now();
    if (now - prev >= delay) {
      func.apply(context, args);
      prev = Date.now();
    }
  };
};
function handle() {
  console.log(Math.random());
}
window.addEventListener("scroll", throttle(handle, 1000));
```

**定时器方法**

```javascript
function throttle(fn) {
  let canRun = true; // 通过闭包保存一个标记
  return function () {
    if (!canRun) return; // 在函数开头判断标记是否为true，不为true则return
    canRun = false; // 立即设置为false
    setTimeout(() => {
      // 将外部传入的函数的执行放在setTimeout中
      fn.apply(this, arguments);
      // 最后在setTimeout执行完毕后再把标记设置为true(关键)表示可以执行下一次循环了。当定时器没有执行的时候标记永远是false，在开头被return掉
      canRun = true;
    }, 500);
  };
}
function sayHi(e) {
  console.log(e.target.innerWidth, e.target.innerHeight);
}
window.addEventListener("resize", throttle(sayHi));
```

**时间戳+定时器**

```javascript
let throttle = function (func, delay) {
  let timer = null;
  let startTime = Date.now();
  return function () {
    let curTime = Date.now();
    let remaining = delay - (curTime - startTime);
    let context = this;
    let args = arguments;
    clearTimeout(timer);
    if (remaining <= 0) {
      func.apply(context, args);
      startTime = Date.now();
    } else {
      timer = setTimeout(func, remaining);
    }
  };
};
function handle() {
  console.log(Math.random());
}
window.addEventListener("scroll", throttle(handle, 1000));
```
