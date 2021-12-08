---
title: 文本内容复制指令 v-copy
urlname: ypo6s9
date: '2021-08-06 16:21:52 +0800'
tags: vue自定义指令
categories: vue自定义指令
---

使用该指令可以复制元素的文本内容。
支持单击复制 `v-copy`、双击复制 `v-copy.dblclick`、点击 icon 复制 `v-copy.icon` 三种模式。默认使用单击复制

# 代码 Code

```javascript
export default {
  bind(el, binding) {
    // 双击触发复制
    if (binding.modifiers.dblclick) {
      el.addEventListener("dblclick", () => handleClick(el.innerText));
      el.style.cursor = "copy";
    }
    // 点击icon触发复制
    else if (binding.modifiers.icon) {
      if (el.hasIcon) return;
      const iconElement = document.createElement("i");
      iconElement.setAttribute("class", "el-icon-document-copy");
      iconElement.setAttribute("style", "margin-left:5px");
      el.appendChild(iconElement);
      el.hasIcon = true;
      iconElement.addEventListener("click", () => handleClick(el.innerText));
      iconElement.style.cursor = "copy";
    }
    // 单击触发复制
    else {
      el.addEventListener("click", () => handleClick(el.innerText));
      el.style.cursor = "copy";
    }
  },
};
function handleClick(text) {
  // 创建元素
  if (!document.getElementById("copyTarget")) {
    const copyTarget = document.createElement("input");
    copyTarget.setAttribute(
      "style",
      "position:fixed;top:0;left:0;opacity:0;z-index:-1000;"
    );
    copyTarget.setAttribute("id", "copyTarget");
    document.body.appendChild(copyTarget);
  }
  // 复制内容
  const input = document.getElementById("copyTarget");
  input.value = text;
  input.select();
  document.execCommand("copy");
  // alert('复制成功')
}
```

# 使用

## 参数 Attributes

| 参数     | 说明                   | 默认值 | 类型   | 可选 |
| -------- | ---------------------- | ------ | ------ | ---- |
| dblclick | 双击复制文本内容       | /      | String | 可选 |
| icon     | 单击 icon 复制文本内容 | /      | String | 可选 |

然后你可以在模板中任何元素上使用新的 `v-copy` `property`，如下：

```javascript
<div v-copy> 单击复制 </div>
<div v-copy.dblclick> 双击复制 </div>
<div v-copy.icon> icon复制 </div>
```
