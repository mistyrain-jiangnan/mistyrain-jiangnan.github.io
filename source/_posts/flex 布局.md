---
title: flex 布局
urlname: btx8xl
date: '2021-11-18 19:18:06 +0800'
tags: flex
categories: flex
---

flex 布局 又称 弹性布局 是前端布局方式中的一种布局方式，较于传统的盒子模型 flex 布局更具有灵活性

## 使用

为一个元素添加 display:flex 这个元素就被称为 flex 容器 通过对这个容器添加不同的属性去控制容器内其他元素的布局方式
为父元素设置 flex 属性，控制子元素在父元素中的布局）
![image (2).png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1637234331848-57fffbca-ac6c-4fd2-a781-6fd685adad97.png#clientId=u7a3a9b31-96e3-4&from=ui&id=ueeac30a2&margin=%5Bobject%20Object%5D&name=image%20%282%29.png&originHeight=363&originWidth=1020&originalType=binary∶=1&size=26338&status=done&style=none&taskId=u3a006775-f5e2-4693-bc63-ce219b987f8)

| 属性                       | 属性值            | 作用                   |
| -------------------------- | ----------------- | ---------------------- |
| 设置当前元素为弹性盒子     |                   |                        |
| _display_:                 | flex;             | 设置当前元素为弹性盒子 |
| 设置主轴方向               |                   |                        |
| _flex-direction_           | row               | X 轴方向 （默认方向）  |
| row-reverse                | 翻转主轴 X 的方向 |                        |
| column                     | 设置主轴为 Y 轴   |                        |
| column-reverse             | 翻转主轴 Y 的方向 |                        |
| 设置子元素在主轴的排列方式 |                   |                        |
| _justify-content_          | flex-start        | 头部对齐（默认）       |

| ​
| flex-end | 尾部对齐 |
| ​
| center | 居中对齐 |
| ​
| space-between | 两端贴边中间平均分配 |
| ​
| space-around | 均分所有空间 |
| 设置子元素侧轴排列 （单行子元素情况） | | |
| _align-items_ | stretch | 拉伸子元素的高到父元素的高度
（默认） |
| flex-start | 头部对齐 | |
| flex-end | 尾部对齐 | |
| center | 居中对齐 | |
| 设置侧轴对齐方式 （多行子元素情况）搭配换行 | | |
| align-content | stretch | 拉伸 （默认） |
| flex-start | 头部对齐 | |
| flex-end | 尾部对齐 | |
| center | 居中对齐 | |
| space-between | 两边贴边中间均分 | |
| space-around | 均分所有剩余空间 | |
| 设置元素换行 （搭配侧轴对齐） | | |
| _flex-wrap_ | nowrap | 默认不换行 |
| ​
| wrap | 设置换行，变为多行对齐方式 |
| felx： 子元素属性 父元素为 flex 布局
子元素设置 flex 属性 将父元素剩余宽度分为 n 份
设置子元素占父元素剩余宽度的几份 | | |
| flex | 数字 | 子元素占父元素剩余宽度的几份 |
| 设置子元素在容器内的排序次序 | | |
| order | 数字 | 值为数字,越大越靠后 |
