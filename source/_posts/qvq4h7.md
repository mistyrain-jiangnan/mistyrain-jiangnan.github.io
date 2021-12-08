---
title: flex实现中间文字，两边横线（1px）
urlname: qvq4h7
date: '2021-08-06 16:30:33 +0800'
tags: []
categories: []
---

### 首先来看组件的需求：

- 左右两边的横线在移动端显示为 1px。
- 中间内容部分可以随意传入文字内容。
- 同时不论文字的多少，两边的横线会随着内容增大而减小，直到内容占据的最大宽度为 40%。 ![](https://cdn.nlark.com/yuque/0/2021/png/12838787/1628238681577-84096e47-92c3-4e15-b67a-b31d8ef7c709.png#align=left&display=inline&height=63&margin=%5Bobject%20Object%5D&originHeight=63&originWidth=943&size=0&status=done&style=none&width=943)
- 根据以上需求，我们来制定步骤。
- 简单的实现需求

```javascript
border-box{
  border: 1px solid #ccc;
  position: relative;
  padding: 25px 15px 10px;
  margin-bottom: 35px;
  margin-top: 12px;
  &:last-child{
    margin-bottom: 0;
  }
  .title {
    line-height: 38px;
    font-size: 15px;
    position: absolute;
    background: #fff;
    padding: 0 30px;
    top: -19px;
    left: 25px;
  }
  .group {
    font-size: 14px;
    line-height: 28px;
    border-bottom: 1px solid #ccc;
    margin-bottom: 15px;
  }
}
```

### 使用

```javascript
<div class="border-box">
  <h3 class="title">未完待续</h3>
</div>
```
