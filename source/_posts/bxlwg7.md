---
title: 文字版的波浪效果
urlname: bxlwg7
date: '2021-08-26 11:02:42 +0800'
tags: []
categories: []
---

简单的实现需求 [https://codepen.io/Chokcoco/pen/jOwEqvR](https://codepen.io/Chokcoco/pen/jOwEqvR)

```javascript
<div class="g-container">
  <p>TEXT WAVE</p>
</div>
```

## css

```javascript
html, body {
    width: 100%;
    height: 100%;
    display: flex;
}

.g-container {
    margin: auto;
}

p {
    position: relative;
    font-size: 120px;
    font-weight: bold;
    background: #fff;
    color: #000;
    overflow: hidden;

    &::before,
    &::after {
        content: "";
        position: absolute;
        top: -923px;
        left: 50%;
        width: 2000px;
        height: 2000px;
        border-radius: 45% 48% 43% 47%;
        transform: translate(-50%, -50%);
        background: rgba(3, 169, 244, .85);
        animation: rotate 10s infinite linear;
        z-index: 1;
        mix-blend-mode: lighten;
    }

    &::after {
        border-radius: 43% 47% 44% 48%;
        animation: rotate 10s infinite .5s linear;
    }
}

@keyframes rotate {
    0% {
        transform: translate(-50%, -50%) rotate(0);
    }
    100% {
        transform: translate(-50%, -50%) rotate(360deg);
    }
}
```

# ![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1629947168502-e9feefa3-4e10-42ad-b936-048d9b266cc4.png#align=left&display=inline&height=442&margin=%5Bobject%20Object%5D&name=image.png&originHeight=442&originWidth=1733&size=31617&status=done&style=none&width=1733)
