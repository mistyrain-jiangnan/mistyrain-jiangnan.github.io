---
title: 多行文本右下角环绕型按钮布局
urlname: tso79m
date: '2021-08-06 16:30:32 +0800'
tags: []
categories: []
---

```javascript
<div class="wrapper">
  <div class="w_text">
  <button class="btn">点击观看</button>
  当我们 new Vue 的时候：
调用Observer，遍历 Vue 每个属性（data、computed、props（子组件）），为每个属性都创建对应的Dep实例。
遍历每个属性时，通过 Object.defineProperty 将所有属性添加到 Vue 实例上，并为 Vue 实例的每一个属性都添加对应的 setter/getter 进行监听。
同时通过Compiler解析模板指令，解析到属性后就 new 一个Watcher并绑定更新函数到 watcher 当中。
Observer 和 Compiler 就通过属性来进行关联，通过 Watcher 来通知变化。
  </div>
</div>
.wrapper {
  width: 800px;
  margin: 50px auto;
  border: 1px solid #eaecef;
  padding: 14px;
  box-sizing: border-box;
  border-radius: 8px;
  box-shadow: 20px 20px 60px #bebebe;
  display: flex;

  .w_text {
    width: 100%;
    line-height: 26px;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 3;
    overflow: hidden;
    &::before {
      content: '';
      float: right;
      // height: calc(100% - 24px);
      height: 100%;
      margin-bottom: -24px;
    }

    .btn {
      background: linear-gradient(90deg,rgba(255,179,1,1) 0%,rgba(254,140,1,1) 100%);
      border-radius: 4px;
      color: #fff;
      border: none;
      cursor: pointer;
      height: 24px;
      float: right;
      clear: both;
    }
  }
}
```

# 用的时候直接使用就可以了
