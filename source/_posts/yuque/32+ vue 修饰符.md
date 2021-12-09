---
title: 32+ vue 修饰符
urlname: tgkgfr
date: '2021-11-24 17:05:01 +0800'
tags: vue
categories: vue
---

#

# 前言

vue 简洁好用体现在很多个地方，比如其内置了 32+修饰符，可以很方便我们阻止冒泡、阻止默认事件、鼠标事件处理、系统键盘事件等等，让我们可以快速搞定业务，简直不要太方便噢！！！
耽误您 15 分钟您可以收获：

1. 32+修饰符（包括事件修饰符、鼠标修饰符、表单修饰符、系统修饰符等等）的含义和使用
1. 如何利用 webpack 动态注册 vue 路由，再也不手写路由配置啦！

文章中例子都放在了 github 源码上，也可以点击直接看例子

# 如何动态注册路由？

文中的每个修饰符例子都由一个页面承载，聪明的你肯定不想手动引入几十个.vue 文件并配置路由.
有什么办法可以帮我们自动完成路由注册呢？

## 1. 文件目录结构

目录结构（已去除其他文件目录）大概如下

```javascript
├── package.json
└── src
    ├── App.vue
    ├── main.js
    ├── router.js
    └── views
        ├── About.vue
        ├── Home.vue
        └── modifiers
            ├── capture.vue
            ├── once.vue
            ├── order.vue
            ├── passive.vue
            ├── prevent.vue
            ├── self.vue
            └── stop.vue
            └── ...
```

## 2. 期望的路由配置

最终给到 vue-router 的配置大概长下面这个样子，每个配置最重要的部分分别是 path、name 和 component

```javascript
[
  {
    "path": "/home",
    "name": "home",
    "component": {
      "name": "Home",
      "methods": {},
      "staticRenderFns": [],
      "_compiled": true,
      "_scopeId": "data-v-fae5bece",
      "beforeCreate": [
        null
      ],
      "beforeDestroy": [
        null
      ],
      "__file": "src/views/Home.vue"
    }
  },
  {
    "path": "/modifiers/capture",
    "name": "modifiersCapture",
    "component": {
      "name": "capture",
      "methods": {},
      "staticRenderFns": [],
      "_compiled": true,
      "_scopeId": "data-v-63b4eeee",
      "beforeCreate": [
        null
      ],
      "beforeDestroy": [
        null
      ],
      "__file": "src/views/modifiers/capture.vue"
    }
  },
  ... // 其他路由配置
]
```

## 3. require.context 实现动态注册路由

借助 webpack require.context 的能力，可以非常方便地实现上面目录到路由配置的映射工作，源码如下

```javascript
const registerRoutes = () => {
  const contextInfo = require.context("./views", true, /.vue$/);
  const routes = contextInfo.keys().map((filePath) => {
    // filePath 形如 ./Home.vue、./modifiers/capture.vue
    // path我们希望是/home、/modifiers/capture
    // 所以需要把开头的./和.vue都替换为空
    const path = filePath.toLowerCase().replace(/^\.|\.vue/g, "");
    // name的话将/home、/modifiers/capture转成小驼峰即可
    // 把开头的/先替换掉，再把第一个/后的单词变成大写就可以了
    const name = path
      .replace(/^\//, "")
      .replace(/\/(\w)/, ($0, $1) => $1.toUpperCase());
    // 通过require去读取.vue文件内容
    const component = require(`./views${filePath.replace(/^\./, "")}`).default;

    return {
      path,
      name,
      component,
    };
  });

  return routes;
};
```

​

**效果**
经过上面的简单处理，动态注册路由就完成啦！您也可以点击 vue-demos 查看效果

# 事件修饰符

## 1. 阻止冒泡的两种方式

```javascript
<template>
  <div class="parent" @click="onClickParent">
    我是爸爸
    <div class="child" @click="onClickChild">
      我是儿子
    </div>
  </div>
</template>

export default {
  name: 'stop',
  methods: {
    onClickParent () {
      console.log('我是爸爸')
    },
    onClickChild () {
      console.log('我是儿子')
    }
  }
}
```

点**击子节点**的时候因为事件冒泡的缘故不仅会打印出我是儿子还会打印我是爸爸。有什么办法可以阻止子节点的**事件冒泡**呢？
![image.png](https://cdn.nlark.com/yuque/0/2021/gif/12838787/1637744717267-b64bf757-8e0a-4acf-8499-ff28cfb0e9a5.gif#clientId=ube0a7b57-a3b9-4&from=paste&id=ufdb6c334&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=70&status=done&style=none&taskId=u3a2a87aa-6cbd-4bcb-a622-0c7fdbb416c)

### 1 .stop

只要加.stop 修饰符即可，阻止事件冒泡的及简方式，很方便是不是。
当添加上.stop 修饰符时，只会出现我是儿子

```javascript
<template>
  <div class="parent" @click="onClickParent">
    我是爸爸
    <div class="child" @click.stop="onClickChild">
      我是儿子
    </div>
  </div>
</template>
```

### 2. event.stopPropagation

当然了，我们也可以通过调用 event.stopPropagation 来阻止冒泡。不过更加推荐修饰符的做法，这样你的函数会更加专注在逻辑处理上，而不用关心 DOM 事件细节

```javascript
export default {
  name: "stop",
  methods: {
    onClickChild(event) {
      console.log("我是儿子");
      event.stopPropagation();
    },
  },
};
```

## 2. 阻止默认事件的两种方式

vue 中阻止冒泡有两种方式，那阻止默认事件呢？

### 1 .prevent

```javascript
<template>
  <div class="prevent">
    <a href="https://juejin.cn/" @click="onNoPrevent">点击跳转掘金</a>




    <a href="https://juejin.cn/" @click.prevent="onPrevent">阻止默认事件，无法跳转掘金</a>
  </div>
</template>

export default {
  name: 'prevent',
  methods: {
    onNoPrevent () {
      console.log('未阻止默认事件')
    },
    onPrevent () {
      console.log('阻止默认事件')
    }
  }
}
```

只要添加.prevent 轻松实现阻止默认事件

### 2.event.preventDefault()

和阻止冒泡一样，我们也可以通过调用事件对象的 preventDefault 方法来阻止默认事件

```javascript
export default {
  name: "prevent",
  methods: {
    onPrevent(event) {
      console.log("阻止默认事件");
      event.preventDefault();
    },
  },
};
```

## 3 .capture

默认情况下，事件流是以冒泡(由里向外)的形式传递的，如果想以捕获（由外向里）的形式应该怎么办呢？

```javascript
<template>
  <div class="capture parent" @click.capture="onClickParent">
    父节点
    <div class="child" @click.capture="onClickChild">自节点</div>
  </div>
</template>

export default {
  name: 'capture',
  methods: {
    onClickParent () {
      console.log('我是父节点')
    },
    onClickChild () {
      console.log('我是子节点')
    }
  }
}
```

​

不加 catpture 修饰符，点击**子节点**会陆续打印我是父节点以及我是子节点，加了之后，则是反过来了

## 4 .self

只有当 event.target 是当前元素自身时才会触发事件回调函数

```javascript
<template>
  <div class="self" @click.self="onClickSelf">
    <div class="inner" @click="onClickInner"></div>
  </div>
</template>

export default {
  name: 'self',
  methods: {
    onClickSelf () {
      console.log('我是self节点')
    },
    onClickInner () {
      console.log('我是inner节点')
    }
  }
}
```

​

不加 self 修饰符的话，点击 inner 节点也会触发 self 的事件，加了之后只有触发事件的元素本身是 self，才会打印出我是 self 节点

## 暂停一下：修饰符的顺序如何理解？

已经回顾了 4 个修饰符，单独使用的时候很容易理解，但是注意官网有这么一句话
![image.png](https://cdn.nlark.com/yuque/0/2021/gif/12838787/1637744717749-e6349a30-d17e-494e-bda7-01e582b7022f.gif#clientId=ube0a7b57-a3b9-4&from=paste&id=ue6c22681&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=70&status=done&style=none&taskId=u4fe3b80c-310b-44fe-8997-3fbdc0a61e3)
image.png
**怎么理解呢？我们来看两个栗子**

```javascript
<template>
  <div class="order">
    <div class="order-0">
      <a href="https://juejin.cn/" class="order-parent" @click.self.prevent="onClickParent">
        我是父节点，会跳转掘金


        <span class="order-child" @click="onClickChild">
          我是子节点
        </span>
      </a>
      <hr />
    </div>
    <div class="order-2">
      <a href="https://juejin.cn/" class="order-parent" @click.prevent.self="onClickParent">
        我是父节点，无法跳转掘金


        <span class="order-child" @click="onClickChild">
          我是子节点
        </span>
      </a>
    </div>
  </div>
</template>

export default {
  name: 'order',
  methods: {
    onClickParent () {
      console.log('我是父节点')
    },
    onClickChild () {
      console.log('我是子节点')
    }
  }
}

```

您可以猜一下，上面的代码会发生什么，以下三点是可以明确的？

1. 首先可以明确的是点击上面和下面的子节点都不会触发父节点的点击事件
1. 点击下面的父节点会打印出**我是父节点**，但是**不会跳转掘金**
1. 点击上面的父节点**会**打印出**我是父节点**,**也不会跳转掘金**

但是点击上面的子节点，父节点会不会跳转至掘金呢？答案是**会**
**为什么？**
a@click.self.prevent="onClickParent"的意思是当点击的元素是 a 元素本身时，会阻止默认事件（可以解释 3，不会发生跳转），并且执行 onClickParent 回调。
而点击 span 元素时，由于冒泡的缘故，点击事件会传递给 a，但是此时 a 会判断出该事件不是由自身触发的也就不会阻止默认事件(此时也就发生跳转了)，当然也不会触发 onClickParent 回调
同理来我们分析一下a@click.prevent.self="onClickParent"
不管是子节点还是自身点击，都是先阻止默认事件，只有当触发点击事件是 a 元素本身时才会执行 onClickParent 回调函数。

**回过头看，你理解事件的顺序含义了吗？**

## 5. once

顾名思义，事件只会触发一次

```javascript
<template>
  <div class="once" @click.once="onClickOnce">
    只触发一次
  </div>
</template>

export default {
  name: 'once',
  methods: {
    onClickOnce () {
      console.log('once，我只会触发一次点击事件回调')
    }
  }
}
```

触发一次点击之后，任我再怎么点，回调怎也不会触发了。

## 6 .native

我们知道在自定义组件上，只能监听自定义事件，一些原生事件（比如 click）是没有办法直接触发的，但是使用.native 修饰符可以帮我们办到这点
**native.vue**

```javascript
<template>
  <div class="native-custom">
    <input type="text" @keydown="onKeydown">
  </div>
</template>

export default {
  name: 'nativeCustom',
  methods: {
    onKeydown () {
      this.$emit('onKeydown')
    }
  }
}
```

**custom.vue**

```javascript
<template>
  <div class="native">
    <!-- 加上.native之后原生事件才得以监听成功 -->
    <NativeCustom @onKeydown="onKeydown" @click.native="onClick" />
  </div>
</template>

import NativeCustom from '../../components/native.vue'

export default {
  name: 'native',
  components: {
    NativeCustom
  },
  methods: {
    onKeydown () {
      console.log('onKeydown')
    },
    onClick () {
      console.log('onClick')
    }
  }
}

```

## 7 .passive

vue 对应 addEventListener 中的 passive 选项提供了 .passive 修饰符

```javascript
<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成 -->
<!-- 这其中包含 `event.preventDefault()` 的情况 -->

<div v-on:scroll.passive="onScroll">...</div>
```

**这个修饰符对于滚动性能的提升，一直没找到合适的例子，跪求广大掘友有例子啊**
**这个修饰符对于滚动性能的提升，一直没找到合适的例子，跪求广大掘友有例子啊**
**这个修饰符对于滚动性能的提升，一直没找到合适的例子，跪求广大掘友有例子啊**

# v-bind 修饰符

## 8 .sync

当我们想要在父组件和子组件之间对某个属性值进行双向绑定时,有什么便捷的方式？是的只要.sync 修饰符即可办到

## 9 .camel

.camel 修饰符允许在使用 DOM 模板时将 v-bind property 名称驼峰化，例如 SVG 的 viewBox property：​

## 10 .prop

关于.prop 修饰符官网只有这句话 .prop 作为一个 DOM property 绑定而不是作为 attribute 绑定。`。
**有啥作用？**

1. 通过自定义属性存储变量，避免暴露数据
1. 防止污染 HTML 结构

比如有以下代码

```javascript
<template>
  <div class="prop">
    <div class="prop-item" :my-name="prop"></div>
    // 最终变成了 <div my-name="hello prop" class="prop-item"></div>
    <div class="prop-item" :my-name.prop="prop2"></div>
    // 最终变成了<div class="prop-item"></div>
    <button @click="onGetResult">获取结果</button>
  </div>
</template>

export default {
  name: 'prop',
  data () {
    return {
      prop: 'hello prop',
      prop2: 'hello prop2'
    }
  },
  methods: {
    onGetResult () {
      const $refProp = this.$refs.prop
      const $refProp2 = this.$refs.prop2

      console.log($refProp.getAttribute('my-name')) // hello prop
      console.log($refProp2.getAttribute('my-name')) // null
    }
  }
}

```

从示例上可以看出未使用.prop 修饰符的 my-name 属性会绑定到 dom 节点的 attribute，从而出现暴露的情况。

# 鼠标修饰符

当咱们想监听用户点击了左键、右键或者中键时也有修饰符可以快捷使用，分别是.left、.right、middle，来看个例子试试
根据 MDN MouseEvent.button，介绍。
在最外层 div.mouse 监听 mousedown 事件，看下用户点击的是鼠标哪个键，三个 button 分别用三个修饰符快捷方式监听左键、中键、右键并打印出 left、middle、right

```javascript
<template>
  <div class="mouse" @mousedown="onMousedown">
    <button @click.left="onClickBtn('left')">left</button>
    <button @click.middle="onClickBtn('middle')">middle</button>
    <button @click.right="onClickBtn('right')">right</button>
  </div>
</template>

export default {
  name: 'mouse',
  mounted () {

  },
  methods: {
    onClickBtn (msg) {
      console.log(msg)
    },
    onMousedown (event) {
      const mosueMsgMap = {
        0: '鼠标左键',
        1: '鼠标中键',
        2: '鼠标右键'
      }
      console.log('点击了', mosueMsgMap[event.button])
    }
  }
}
```

**没有带鼠标回来，中键点击暂时不能演示，后续会补上**
![image.png](https://cdn.nlark.com/yuque/0/2021/gif/12838787/1637744718538-11650970-9341-438b-b845-a67688ff5897.gif#clientId=ube0a7b57-a3b9-4&from=paste&id=u4ad4c271&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=70&status=done&style=none&taskId=u74db735d-39da-43ad-b642-b24eee0ddf1)
mouse.gif

## 11 .left

同上例子，监听鼠标左键点击

## 12 .right

同上例子，监听鼠标右键点击

## 13 .middle

同上例子，监听鼠标中键点击

# 表单相关修饰符

## 14 .trim

对于输入的内容，希望可以过滤首尾空格应该怎么做呢？

```javascript
<template>
  <div class="trim">
    <div class="trim-item">
      <input type="text" v-model="name">
      <p>用户名：<span>{{ name }}</span></p>
    </div>
    <div class="trim-item">
      <input type="text" v-model.trim="name2">
      <p>用户名2：<span>{{ name2 }}</span></p>
    </div>
  </div>
</template>

export default {
  name: 'trim',
  data () {
    return {
      name: '',
      name2: '',
    }
  },
  watch: {
    name (newVal) {
      console.log(`'----${newVal}----'`)
    },
    name2 (newVal) {
      console.log(`'----${newVal}----'`)
    },
  }
}
```

**.trim 修饰符可以很方便做到**

## 15 .lazy

v-model 大家都很熟悉，默认情况下，每次 input 事件触发的时候都会将输入框的值与其绑定的数据进行实时同步。但是如果想要实现光标离开的时候再更新数据如何实现呢？
**思路 1：** 绑定 change 事件，在事件回调中手动获取 target 的值
**思路 2：** 直接使用.lazy 修饰符即可达到效果

```javascript
<template>
  <div class="lazy">
    <div class="lazy-item">
      <input type="text" v-model="text">
      <p>无.lazy: {{ text }}</p>
    </div>

    <div class="lazy-item">
      <input type="text" v-model.lazy="text2">
      <p>.lazy: {{ text2 }}</p>
    </div>
  </div>
</template>

export default {
  name: 'lazy',
  data () {
    return {
      text: '',
      text2: ''
    }
  }
}
```

可以看到添加了.lazy 修饰符之后，第二个输入框输入的值不会实时反应在下面，而是光标离开实，text2 的数据才更新了

## 16 .number

我们知道 input 输入框的 type 哪怕是 number 得到的值的类型也是 string，如果我们想直接拿到 number 类型的数据，有不想麻烦的手动转换应该怎么办呢？

```javascript
<template>
  <div class="number">
    <div class="number-item">
      <p>无.number </p>
      <input type="number" v-model="number">
    </div>
    <div class="number-item">
      <p>type:text .number </p>
      <input type="text" v-model.number="number1">
    </div>
    <div class="number-item">
      <p>type:number .number </p>
      <input type="number" v-model.number="number2">
    </div>
  </div>
</template>

export default {
  name: 'lazy',
  data () {
    return {
      number: 0,
      number1: '',
      number2: '',
    }
  },
  watch: {
    number (newVal) {
      console.log(typeof newVal, newVal)
    },
    number1 (newVal) {
      console.log(typeof newVal, newVal)
    },
    number2 (newVal) {
      console.log(typeof newVal, newVal)
    },
  }
}
```

1. 第一个输入框的类型是 number，但是得到的值是 string
1. 第二个输入框的类型是 text,但是添加了 number 修饰符，得到的值可以是 number（如果这个值无法被 parseFloat() 解析，则会返回原始的值。）
1. 第三个输入框的类型是 number，最后得到的值也是 number

![image.png](https://cdn.nlark.com/yuque/0/2021/gif/12838787/1637744718730-63ac6979-605e-4f25-b99e-4c4b0c6e222f.gif#clientId=ube0a7b57-a3b9-4&from=paste&id=u91c65a41&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=70&status=done&style=none&taskId=u41d07f12-da60-4a2b-8499-f0209ae7331)
number.gif

# 系统修饰符

当点击事件或者键盘事件需要系统键同时按下才触发时.ctrl、.alt、.shift、.meta 可以帮大忙噢！
**如下代码**

1. 全局监听 keydown 事件，尝试看.ctrl、.alt、.shift、.meta 是否被按下
1. 分别给四个按钮加上 .ctrl、.alt、.shift、.meta 修饰符并配合点击事件，验证是否同时按下指定按键，再点击才会生效

**注明：电脑 ctrl 键 + 点击估计和浏览器快捷配置冲突了，导致没触发**

```javascript
<template>
  <div class="system">
    <p>{{ msg }}</p>
    <div class="buttons">
      <button @click.ctrl="onClickButon('ctrl')">ctrl</button>
      <button @click.alt="onClickButon('alt')">alt</button>
      <button @click.shift="onClickButon('shift')">shift</button>
      <button @click.meta="onClickButon('meta')">meta</button>
    </div>
  </div>
</template>

export default {
  name: 'system',
  data () {
    return {
      msg: ''
    }
  },
  mounted () {
    this.onListenSystemKeyDown()
  },
  methods: {
    onListenSystemKeyDown () {
      document.addEventListener('keydown', (event) => {
        let msg = '按下了'

        if (event.ctrlKey) {
          msg += 'ctrl键'
        } else if (event.altKey) {
          msg += 'alt键'
        } else if (event.shiftKey) {
          msg += 'shift键'
        } else if (event.metaKey) {
          msg += 'meta键'
        } else {
          msg += '其他键'
        }

        this.msg = msg
      }, false)
    },
    onClickButon (key) {
      console.log(`只有同时按下${key}键，点击事件才会发生`)
    }
  }
}
```

## 17 .ctrl

仅在按下 ctrl 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 18 .alt

仅在按下 alt 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 19 .shift

仅在按下 shift 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 20 .meta

仅在按下 meta 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 21 .exact

严格来说这.exact 不属于系统修饰符，只是上面例子的写法有一个现象，同时按下几个系统修饰键（例如 alt 和 shift）既可以触发.alt 也可以触发.shift。
还是用上面的例子，看一下下面的 gif, 此时我同时按下了 alt 和 shift，对应的两个事件都可以触发
![image.png](https://cdn.nlark.com/yuque/0/2021/gif/12838787/1637744718900-f7f1c0a9-53bb-4896-8df6-7b136c77f55f.gif#clientId=ube0a7b57-a3b9-4&from=paste&id=uaac7048a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=70&status=done&style=none&taskId=u98a03c13-3c8f-42ff-aaf6-d3be97c1223)
system2.gif

1. **只想某个系统修饰键按下时才触发点击**
1. **没有任何系统修饰符被按下的时候才触发点击**

要实现上面的需求.exact 就派上用场了，用上面的例子稍作改造

```javascript
<template>
  <div class="extra">
    <p>{{ msg }}</p>
    <div class="buttons">
      <button @click.ctrl.exact="onClickButon('ctrl')">ctrl</button>
      <button @click.alt.exact="onClickButon('alt')">alt</button>
      <button @click.shift.exact="onClickButon('shift')">shift</button>
      <button @click.meta.exact="onClickButon('meta')">meta</button>
      <button @click.exact="onClickButon('非系统键')">非系统键</button>
    </div>
  </div>
</template>

export default {
  name: 'extra',
  data () {
    return {
      msg: ''
    }
  },
  mounted () {
    this.onListenSystemKeyDown()
  },
  methods: {
    onListenSystemKeyDown () {
      document.addEventListener('keydown', (event) => {
        let msg = '按下了'

        if (event.ctrlKey) {
          msg += 'ctrl键'
        } else if (event.altKey) {
          msg += 'alt键'
        } else if (event.shiftKey) {
          msg += 'shift键'
        } else if (event.metaKey) {
          msg += 'meta键'
        } else {
          msg += '其他键'
        }

        this.msg = msg
      }, false)
    },
    onClickButon (key) {
      console.log(`只有同时按下${key}键，点击事件才会发生`)
    }
  }
}
```

# 按键修饰符

在监听键盘事件时，我们经常需要检查详细的按键再执行对应的逻辑，vue 也为我们内置了至少 11+的按键修饰符。
如下代码，我们分别给 enter、tab、delete 等按键指定了 keydown 事件，当在指定的输入框中按下指定的键盘，会打印出 enter、tab、delete 等，其他按键在输入框中无法触发该 console

```javascript
<template>
  <div class="key-modifiers">
    <div class="key-modifiers-item">
      enter:
      <input type="text" @keydown.enter="onKeydown('enter')">
    </div>
    <div class="key-modifiers-item">
      tab:
      <input type="text" @keydown.tab="onKeydown('tab')">
    </div>
    <div class="key-modifiers-item">
      delete:
      <input type="text" @keydown.delete="onKeydown('delete')">
    </div>
    <div class="key-modifiers-item">
      esc:
      <input type="text" @keydown.esc="onKeydown('esc')">
    </div>
    <div class="key-modifiers-item">
      space:
      <input type="text" @keydown.space="onKeydown('space')">
    </div>
    <div class="key-modifiers-item">
      up:
      <input type="text" @keydown.up="onKeydown('up')">
    </div>
    <div class="key-modifiers-item">
      down:
      <input type="text" @keydown.down="onKeydown('down')">
    </div>
    <div class="key-modifiers-item">
      left:
      <input type="text" @keydown.left="onKeydown('left')">
    </div>
    <div class="key-modifiers-item">
      right:
      <input type="text" @keydown.right="onKeydown('right')">
    </div>

    <div class="key-modifiers-item">
      page-down:
      <input type="text" @keydown.page-down="onKeydown('page-down')">
    </div>
    <div class="key-modifiers-item">
      page-up:
      <input type="text" @keydown.page-up="onKeydown('page-up')">
    </div>
  </div>
</template>

export default {
  name: 'keyModifiers',
  methods: {
    onKeydown (keyName) {
      console.log(keyName)
    }
  }
}

```

## 22 .enter

在按下 enter 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 23 .tab

在按下 tab 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 24 .delete

在按下 delete 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 25 .esc

在按下 esc 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 26 .space

在按下 space 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 27 .up

在按下 up 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 28 .down

在按下 down 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 29 .left

在按下 left 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 30 .right

在按下 right 按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 31 .page-down

在按下(fn + down)按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

## 32 .page-up

在按下(fn + up)按键时才触发鼠标或键盘事件的监听器，详细例子请看上面

# 如何自定义按键修饰符

vue 本身给我们内置了很多实用的按键修饰符，大部分情况下可以满足我们的日常需求了，那么有没有办法可以自定义按键修饰符呢？
通过以下配置即可定义一个属于我们自己的按键修饰符, 比如我们定义 q 为按下 q 的快捷键。

```javascript
Vue.config.keyCodes = {
  q: 81
}

<div class="custom">
  <input type="text" @keydown.q="f1Keydown">
</div>

export default {
  name: 'custom',
  methods: {
    f1Keydown () {
      console.log('按下了q')
    }
  }
}
```
