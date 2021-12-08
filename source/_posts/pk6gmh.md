---
title: 特殊字符校验自定义指令
urlname: pk6gmh
date: '2021-08-06 16:45:12 +0800'
tags: []
categories: []
---

# v-character

支持 input 框的特殊字符校验

# 代码 Code

```javascript
import { specialCharacterReg } from './regRules'  //引入正则规则
const findEle = (parent, type) => {
  return parent.tagName.toLowerCase() === type ? parent : parent.querySelector(type)
}

const trigger = (el, type) => {
  const e = document.createEvent('HTMLEvents')
  e.initEvent(type, true, true)
  el.dispatchEvent(e)
}

const character =
  Vue.directive('character', {
    bind: function (el) {
      // 正则规则可根据需求自定义
     // var specialCharacterReg = new RegExp(/[`~!@#$%^&*()_\-+=<>?:"{}|,./;'\\[\]·~！@#￥%……&*（）——\-+={}|《》？：“”【】、；‘'，。、]/ig)
      const $inp = findEle(el, 'input')
      el.$inp = $inp
      $inp.handle = function () {
        const val = $inp.value
        $inp.value = val.replace(specialCharacterReg, '')
        trigger($inp, 'input')
      }
      $inp.addEventListener('keyup', $inp.handle)
    },
    unbind: function (el) {
      el.$inp.removeEventListener('keyup', el.$inp.handle)
    } }
  )
export { character }
怎么使用它？
在main.js里面挂载它
例如
import '@/utils/character'
```
