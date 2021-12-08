---
title: 工作中用到的函数
urlname: acu5in
date: '2021-11-18 17:44:43 +0800'
tags: 函数
categories: 函数
---

## 计算服务器时间

```javascript
export function isUndefined(value) {
  return typeof value === "undefined";
}
export function getTSL() {
  var tsl = new Date().getTime();
  if (!isUndefined(config.timeDifference)) {
    tsl = tsl - config.timeDifference;
  }
  return tsl;
}
const getTimeFunc = async function () {
  await getServiceTime().then((res) => {
    config.timeDifference = new Date().getTime() - res;
  });
};
// 获取本地时间与服务器时间的差值
if (isUndefined(config.timeDifference)) {
  await getTimeFunc();
}
```

## 2.保留到小数点以后 n 位

```javascript
// 保留小数点以后几位，默认2位
export function cutNumber(number, no = 2) {
  if (typeof number != "number") {
    number = Number(number);
  }
  return Number(number.toFixed(no));
}
```

## 3.生成随机 16 进制颜色

```javascript
"#" +
  Math.floor(Math.random() * 0xffffff)
    .toString(16)
    .padEnd(6, "0");
```

## 4.使用 history.back() 可以创建一个浏览器“返回”按钮

```javascript
<button onclick="history.back()">返回</button>
```

## 5. 检查 Caps Lock 是否打开

```javascript
您可以使用 KeyboardEvent.getModifierState() 来检测是否 Caps Lock 打开。
const passwordInput = document.getElementById('password');
passwordInput.addEventListener('keyup', function (event) {
    if (event.getModifierState('CapsLock')) {
        // CapsLock 已经打开了
    }
});

```

## 6. 复制到剪贴板

```javascript
您可以使用 Clipboard API 创建“复制到剪贴板”功能：
function copyToClipboard(text) {
    navigator.clipboard.writeText(text);
}
```

# js 代码片段

## dom

### 检查一个元素是否被聚焦

```javascript
const hasFocus = (ele) => ele === document.activeElement;
```

### 获取一个元素的所有兄弟元素

```javascript
const siblings = (ele) =>
  .slice.call(ele.parentNode.children).filter((child) => child !== ele);
```

### 获取选中文本

```javascript
const getSelectedText = () => window.getSelection().toString();
```

### 清除所有 cookies

```javascript
const cookies = document.cookie.split(';').map((item) =>
item.split('=')).reduce((acc, [k, v]) => (acc[k.trim().replace('"', '')] = v) && acc, {});
将 cookie 转换为对象
const cookies = document.cookie.split(';').map((item) => item.split('=')).reduce((acc, [k, v]) => (acc[k.trim().replace('"', '')] = v) && acc, {});
```

## 数组

### 比较两个数组

```javascript
// `a` 和 `b` 都是数组
const isEqual = (a, b) => JSON.stringify(a) === JSON.stringify(b);

// 或者
const isEqual = (a, b) =>
  a.length === b.length && a.every((v, i) => v === b[i]);

// 事例
isEqual([1, 2, 3], [1, 2, 3]); // true
isEqual([1, 2, 3], [1, "2", 3]); // false
```

### 将对象数组转换为单个对象

```javascript
const toObject = (arr, key) =>
  arr.reduce((a, b) => ({ ...a, [b[key]]: b }), {});
// Or
const toObject = (arr, key) =>
  Object.fromEntries(arr.map((it) => [it[key], it]));

// 事例
toObject(
  [
    { id: "1", name: "Alpha", gender: "Male" },
    { id: "2", name: "Bravo", gender: "Male" },
    { id: "3", name: "Charlie", gender: "Female" },
  ],
  "id"
);
/*
{
'1': { id: '1', name: 'Alpha', gender: 'Male' },
'2': { id: '2', name: 'Bravo', gender: 'Male' },
'3': { id: '3', name: 'Charlie', gender: 'Female' }
}
*/
```

### 根据对象数组的属性进行计数

```javascript
const countBy = (arr, prop) =>
  arr.reduce(
    (prev, curr) => ((prev[curr[prop]] = ++prev[curr[prop]] || 1), prev),
    {}
  );

// 事例
countBy(
  [
    { branch: "audi", model: "q8", year: "2019" },
    { branch: "audi", model: "rs7", year: "2020" },
    { branch: "ford", model: "mustang", year: "2019" },
    { branch: "ford", model: "explorer", year: "2020" },
    { branch: "bmw", model: "x7", year: "2020" },
  ],
  "branch"
);

// { 'audi': 2, 'ford': 2, 'bmw': 1 }
```

### 检查数组是否为空

```javascript
const isNotEmpty = (arr) => Array.isArray(arr) && Object.keys(arr).length > 0;
// 事例
isNotEmpty([]); // false
isNotEmpty([1, 2, 3]); // true
```

## 对象

### 检查多个对象是否相等

```javascript
const isEqual = (...objects) =>
  objects.every((obj) => JSON.stringify(obj) === JSON.stringify(objects[0]));
// 事例

console.log(isEqual({ foo: "bar" }, { foo: "bar" })); // true
console.log(isEqual({ foo: "bar" }, { bar: "foo" })); // false
```

### 反转对象的键和值

```javascript
const invert = (obj) =>
  Object.keys(obj).reduce((res, k) => Object.assign(res, { [obj[k]]: k }), {});
// 或
const invert = (obj) =>
  Object.fromEntries(Object.entries(obj).map(([k, v]) => [v, k]));
// 事例
invert({ a: "1", b: "2", c: "3" }); // { 1: 'a', 2: 'b', 3: 'c' }
```

### 从对象中删除所有 null 和 undefined 的属性

```javascript
const removeNullUndefined = (obj) =>
  Object.entries(obj).reduce(
    (a, [k, v]) => (v == null ? a : ((a[k] = v), a)),
    {}
  );

// 或

const removeNullUndefined = (obj) =>
  Object.entries(obj)
    .filter(([_, v]) => v != null)
    .reduce((acc, [k, v]) => ({ ...acc, [k]: v }), {});

// 或

const removeNullUndefined = (obj) =>
  Object.fromEntries(Object.entries(obj).filter(([_, v]) => v != null));

// 例子
removeNullUndefined({
  foo: null,
  bar: undefined,
  fuzz: 42,
});
// { fuzz: 42 }
```

### 根据对象的属性对其进行排序

```javascript
Object.keys(obj)
  .sort()
  .reduce((p, c) => ((p[c] = obj[c]), p), {});

// 事例
const colors = {
  white: "#ffffff",
  black: "#000000",
  red: "#ff0000",
  green: "#008000",
  blue: "#0000ff",
};

sort(colors);

/*
{
  black: '#000000',
  blue: '#0000ff',
  green: '#008000',
  red: '#ff0000',
  white: '#ffffff',
}
*/
```

## 字符串

### 检查路径是否是相对路径

```javascript
const isRelative = (path) => !/^([a-z]+:)?[\\/]/i.test(path);

// 例子
isRelative("/foo/bar/baz"); // false
isRelative("C:\\foo\\bar\\baz"); // false
isRelative("foo/bar/baz.txt"); // true
isRelative("foo.md"); // true
```

### 将字符串的第一个字符改为小写

```javascript
const lowercaseFirst = (str) => `${str.charAt(0).toLowerCase()}${str.slice(1)}`;

// 例子
lowercaseFirst("Hello World"); // 'hello World'
```

### 重复一个字符串

```javascript
const repeat = (str, numberOfTimes) => str.repeat(numberOfTimes);

// 例子
repeat("ab", 3);
// ababab
```

### 金额小写转大写

```javascript
参数:n 小写金额 number类型
返回值: 返回生成的大写金额

export const moneyToCapitalsFun = function( n ) {
    // 小数部分
    let fraction = ['角', '分'];
    // 数字部分
    let digit = [
        '零', '壹', '贰', '叁', '肆',
        '伍', '陆', '柒', '捌', '玖'
    ];
    // 单位
    let unit = [
        ['元', '万', '亿'],
        ['', '拾', '佰', '仟']
    ];
    // 是否为负值，if T 前边加 ’欠‘
    let head = n < 0 ? '欠' : '';
    // 取绝对值
    n = Math.abs(n);
    let s = '';
    // 小数计算
    for (let i = 0; i < fraction.length; i++) {
        s += (digit[Math.floor(n * 10 * Math.pow(10, i)) % 10] + fraction[i]).replace(/零./, '');
    }
    s = s || '整';
    n = Math.floor(n);
    // 单位计算
    for (let i = 0; i < unit[0].length && n > 0; i++) {
        let p = '';
        for (let j = 0; j < unit[1].length && n > 0; j++) {
            p = digit[n % 10] + unit[1][j] + p;
            n = Math.floor(n / 10);
        }
        s = p.replace(/(零.)*零$/, '').replace(/^$/, '零') + unit[0][i] + s;
    }
    return head + s.replace(/(零.)*零元/, '元')
        .replace(/(零.)+/g, '零')
        .replace(/^整$/, '零元整');
  }
```

##

## Dates

### 给一个小时添加“am/pm”后缀

```javascript
// `h` is an hour number between 0 and 23
const suffixAmPm = (h) =>
  `${h % 12 === 0 ? 12 : h % 12}${h < 12 ? "am" : "pm"}`;

// 例子
suffixAmPm(0); // '12am'
suffixAmPm(5); // '5am'
suffixAmPm(12); // '12pm'
suffixAmPm(15); // '3pm'
suffixAmPm(23); // '11pm'
```

### 计算两个日期之间的不同天数

```javascript
const diffDays = (date, otherDate) =>
  Math.ceil(Math.abs(date - otherDate) / (1000 * 60 * 60 * 24));

// 例子
diffDays(new Date("2014-12-19"), new Date("2020-01-01")); // 1839
```

## 其它

```javascript
检查代码是否在Node.js中运行
const isNode = typeof process !== 'undefined' && process.versions != null &&
  process.versions.node != null;

检查代码是否在浏览器中运行
const isBrowser = typeof window === 'object' && typeof document === 'object';

将URL参数转换为对象
const getUrlParams = (query) =>Array.from(new   URLSearchParams(query)).reduce((p, [k, v]) => Object.assign({}, p, { [k]: p[k]   ? (Array.isArray(p[k]) ? p[k] : [p[k]]).concat(v) : v }),{});

// 例子
getUrlParams(location.search); // Get the parameters of the current URL
getUrlParams('foo=Foo&bar=Bar'); // { foo: "Foo", bar: "Bar" }

// Duplicate key
getUrlParams('foo=Foo&foo=Fuzz&bar=Bar'); // { foo: ["Foo", "Fuzz"], bar: "Bar" }

黑暗检测模式
const isDarkMode = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;

将RGB转换为十六进制
const rgbToHex = (r, g, b) =>
   "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);

// 例子
rgbToHex(0, 51, 255); // #0033ff

生成随机IP地址
const randomIp = () => Array(4).fill(0)
.map((_, i) => Math.floor(Math.random() * 255) + (i === 0 ? 1 : 0))
.join('.');

// 例子
randomIp(); // 175.89.174.131

使用 Node crypto 模块生成随机字符串
const randomStr = () => require('crypto').randomBytes(32).toString('hex');

```
