---
title: 基于ant-design-vue封装的全局loading
urlname: euhuhq
date: '2021-08-10 11:02:15 +0800'
tags: []
categories: []
---

### Dom 结构

```javascript
<template>
  <div class="upload-spin" v-if="loading" >
    <a-spin size="large" :tip="showText" :style="{fontSize:size+'px'}"></a-spin>
  </div>
</template>
```

## JavaScript

```javascript
export default {
  props: {
    showText: {
      type: String,
      default: "生成中",
    },
    loading: {
      type: Boolean,
      required: true,
      default: false,
    },
    size: {
      type: String,
      default: "16",
    },
  },
};
```

## Less

```javascript
.upload-spin {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  text-align: center;
  background: rgba(0,0,0,0.3);
  z-index: 999;
  .ant-spin {
    position: relative;
    top: 250px;
  }
}
```
