---
title: 插件形式注册全局组件
urlname: yynwou
date: '2021-11-18 19:08:50 +0800'
tags: vue自定义组件
categories: vue自定义组件
---

## 使用 vue.use() 以插件形式注册全局组件

vue.use( ) 可以扩展 vue 的功能
例如：vuex ，组件库，vurRouter（路由） 都是通过 vue.use()进行扩展的
那么，我们也可以通过它把自己的代码扩展到 vue 中

## vue.ues（）解析

vue.use ( ) 它的参数是一个对象 ，在这个对象中存有一个 install() 方法
在这个方法内进行扩展
在 vue2 中 这个方法的参数是 vue 的构造函数 Vue
在 vue3 中 这个方法的参数是 vue 的应用实例 app

## 注册全局组件

```javascript
// 单独定义js文件
// 先引入 组件
 // vue2
export default{
 install(Vue){
   Vue.component(自定义标签 ,自定义组件名)
  }
}
 // vue3
export default {
 install(app){
   app.component(自定义标签 ,自定义组件名)
  }
}

// main.js中 导入扩展文件 注册
 // vue2
Vue.use(功能名)
 // vue3中
createApp(App).use(store).use(router).use(组件).mount('#app')
```

## 自动注册

使用上述方法，手动注册在组件多的情况下会耗费大量的时间
使用 webpack 提供的方法加载当前文件夹下所有的 .vue 文件

```javascript
// context(目录路径，是否加载子目录，加载文件的匹配正则)
const importFn = require.context("./", false, /\.vue$/);
export default {
  install(app) {
    // importFn.keys()拿取所有组件 通过循环精选注册
    importFn.keys().forEach((path) => {
      // 导入组件
      const component = importFn(path).default;
      // 进行注册 以组件名作为自定义标签名
      app.component(component.name, component);
    });
  },
};
```

## 配置完成后只要在这个文件夹中添加的组件都会被注册为全局组件

​

## 注意：所有的自定义标签名都会使用组件名 ，就是组件的 name 属性，注意为自己的组件添加这个属性

​
