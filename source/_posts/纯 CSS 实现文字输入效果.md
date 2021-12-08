---
title: 纯 CSS 实现文字输入效果
urlname: gv5gfw
date: '2021-09-16 18:43:03 +0800'
tags: css
categories: css
---

[
](https://codepen.io/Chokcoco/pen/jOwEqvR)

```javascript
<h1>Pure CSS Typing animation.</h1>
```

## css

```javascript
body {
    background: black;
    color: #fff;
}

h1 {
    font: bold 200% Consolas, Monaco, monospace;
    border-right: 0.1em solid;
    width: 26ch;
    margin: 2em 1em;
    white-space: nowrap;
    overflow: hidden;
    animation: typing 3s steps(26, end),
        cursor-blink 0.3s step-end infinite alternate;

}

@keyframes typing {
    from {
        width: 0;
    }
}

@keyframes cursor-blink {
    50% {
        border-color: transparent;
    }
}
```

#
