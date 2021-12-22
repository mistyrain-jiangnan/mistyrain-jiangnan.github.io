---
title: D3可视化，SVG介绍
urlname: ob2y8t
date: '2021-12-22 13:15:26 +0800'
tags: 可视化
categories: 可视化
---

## D3 可视化

### 选择集

D3 是实现数据可视化，仍然离不开传统 DOM 的选择和操作，也因此，D3 提供了类似 Jquery 的 DOM 操作指令：

- d3.select：选择第一个指定元素
- d3.selectAll ： 选择所有的元素

```
const svg = d3.select('svg') //选择svg
const p = svg.selectAll('p') //选择svg下所有的p标签
```

当然，可以使用#id 以及 .class 对 id 和类进行选择

### 查看状态

d3.select 和 d3.selectAll 返回的都是选择集，添加、删除以及修改都需要用到选择集，查看状态有三个函数可以使用：

- selection.empty() 选择集为空，返回 true，否则返回 false
- selection.node() 返回第一个非空元素，如果选择集为空，返回 null
- selection.size() 返回选择集中的元素个数

### 设定和获取属性

使用 select 或 selectAll 选择后，可以通过 attr 获取和设定属性，可以使用 append 方法添加元素

```
const svg = select('svg');
svg.append('circle')
.attr('r','30')
```

### SVG 内置图形

#### 1、矩形

- rect 元素的 width 和 height 属性可定义矩形的高度和宽度
- x 属性定义矩形的左侧位置（例如，x=”0” 定义矩形到浏览器窗口左侧的距离是 0px）
- y 属性定义矩形的顶端位置（例如，y=”0” 定义矩形到浏览器窗口顶端的距离是 0px）
- rx 和 ry 属性可使矩形产生圆角
- style 属性用来定义 CSS 属性
- CSS 的 fill 属性定义矩形的填充颜色（rgb 值、颜色名或者十六进制值）
- CSS 的 stroke-width 属性定义矩形边框的宽度
- CSS 的 stroke 属性定义矩形边框的颜色
- CSS opacity 属性用于定义了元素的透明值 (范围: 0 到 1)。
- CSS 的 fill-opacity 属性定义填充颜色透明度（合法的范围是：0 - 1）
- CSS 的 stroke-opacity 属性定义笔触颜色的透明度（合法的范围是：0 - 1）

#### 2、圆形

- cx 和 cy 属性定义圆点的 x 和 y 坐标。如果省略 cx 和 cy，圆的中心会被设置为(0, 0)
- r 属性定义圆的半径

#### 3、椭圆

- CX 属性定义的椭圆中心的 x 坐标
- CY 属性定义的椭圆中心的 y 坐标
- RX 属性定义的水平半径
- RY 属性定义的垂直半径

#### 4、直线

- x1 属性在 x 轴定义线条的开始
- y1 属性在 y 轴定义线条的开始
- x2 属性在 x 轴定义线条的结束
- y2 属性在 y 轴定义线条的结束

#### 5、曲线

- points 属性定义多边形每个角的 x 和 y 坐标
- 是用于创建任何只有直线的形状

#### 6、多边形

- points 属性定义多边形每个角的 x 和 y 坐标

#### 7、路径

- M = moveto(M X,Y) ：将画笔移动到指定的坐标位置
- L = lineto(L X,Y) ：画直线到指定的坐标位置
- H = horizontal lineto(H X)：画水平线到指定的 X 坐标位置
- V = vertical lineto(V Y)：画垂直线到指定的 Y 坐标位置
- C = curveto(C X1,Y1,X2,Y2,ENDX,ENDY)：三次贝赛曲线
- S = smooth curveto(x2 y2 x y)光滑三次贝塞尔曲线到
- Q = quadratic Belzier curve(Q X,Y,ENDX,ENDY)：二次贝赛曲线
- T = smooth quadratic Belzier curveto(T ENDX,ENDY)：光滑二次贝塞尔曲线到
- A = elliptical Arc(A RX,RY,XROTATION,FLAG1,FLAG2,X,Y)：arc 椭圆弧
- Z = closepath()：关闭路径
  `如：M 起点X，起点Y L（直线）终点X，终点Y H（水平线）终点X V（垂直线）终点Y M 10,20 L 80,50 M 10,20 V 50 M 10,20 H 90`

如果指令字母是大写的，例如 M, 则表示坐标位置是绝对位置；如果指令字母小写的，例如 m, 则表示坐标位置是相对位置。

#### 8、文本

- x 属性定义矩形的左侧位置（例如，x=”0” 定义矩形到浏览器窗口左侧的距离是 0px）
- y 属性定义矩形的顶端位置（例如，y=”0” 定义矩形到浏览器窗口顶端的距离是 0px）
  `<text x="10" y="15">Text travels along any path that you define for it.</text>`
  ####9、容器元素用法
- 链接元素
  svg 的链接元素 a 和 html 的链接元素 a 很相似，都是指向一个超链接。
  在 svg 的 a 元素中，可以包含任何的形状
  注意： 在获取 svg 的 a 元素时，为了区分 html 的 a 元素，要加命名空间
  `//css @namespace svg url(http://www.w3.org/2000/svg); svg|a {}`
- defs
  defs 元素通常用来存储在后续将会用到的图像，其 defs 中的图像，不会直接呈现，必须在后续通过其他元素 引用才能呈现。
  相当于在 js 中，封装的一个方法，供其他地方调用。 在这里，是封装了一段 html，供后续的 html 调用。
  一般情况下，defs 中用来定义过滤，渐变，填充(pattern)等效果。
- g
  元素是一个用于对其他 SVG 元素进行分组的容器。
  在元素上设置的属性和变换，会统一影响 g 分组中的所有元素，会把 g 元素当成一个整体处理。
  和前面的 defs 类似，只是中的元素会直接作为一个整体呈现，可以不需要引用就能呈现，当然也能被 use 复制。
- mask
  mask 元素定义了一个 alpha 通道，用于将当前对象合成到背景中，在其他元素上通过 mask 属性来引用。
  其本身不会呈现，必须被其他元素引用才能呈现。
- marker
  marker 元素定义了用于在给定的、、或元素上绘制箭头或 多点标记 的图形。
  在元素上使用 marker-start, marker-mid, 和 marker-end 属性，来引用 marker 元素。
  marker 有如下属性:
  markerHeight: 定义 marker 元素的高度
  markerWidth: 定义 marker 元素的宽度
  markerUnits: 该属性为 markerWidth、markerHeight 以及 marker 的内容 定义了坐标系统。 userSpaceOnUse|strokeWidth
  orient：定义 marker 相对于寄宿元素的方向，值有 auto|auto start-reverse|。
  refX: marker 元素相对于 X 轴的偏移。和正常坐标系相反，正值向左，负值向右。
  refY：marker 元素相对于 Y 轴的偏移。和正常坐标系相反，正值向上，负值向下。
  viewBox： 当前 marker 片段定义 SVG 视图端口的边界。
  preserveAspectRatio：定义了如果 marker 片段嵌入到具有不同纵横比的容器中，则必须如何对其进行变形。
  注意：
  如果一个元素定义了 marker-start，那么就在开头的位置添加 marker，
  如果一个元素定义了 marker-mid，那么就在所有中间位置添加 marker，
  如果一个元素定义了 marker-end，那么就在结尾位置添加 marker。

可以定义多个，那么也就添加多个。
#### 10、其它

- 1、image 图像
  x 表示图像左上角在水平方向的位移，
  y 表示图像做商检在竖直方向的位移，
  height，width 分别为 image 的高度和宽度。
  注意：默认图像不会缩放，即使定义了 height，width，也不会缩放铺满剩余区域
- 2、stroke 属性
  SVG 提供了一个范围广泛 stroke 属性。这几个属性多用来定义线条的样式。
  stroke. 线条颜色
  stroke-width 线条粗细
  stroke-linecap 线条闭合方式
  stroke-dasharray 线条虚线数值组
- 3、use
  该元素从 SVG 文档中获取节点，并在其他位置复制它们。
  当 use 复制其他元素后，还能对复制的元素进行属性的覆盖。
  x, y, width, height，href 这几个属性，不管源 元素是否有设置，都可以覆盖。
  而其他属性，如果源 元素已经设置，则无法覆盖，如果没有设置，则可以再 use 上设置。
  可以看到设置的 x 属性覆盖成功了，第二个 use 的 fill 也设置成功了，但是第三个 use 的 stroke 没有变成红色，因为 circle 已经指定了，所以无法覆盖。
- 4、事件
  最常用的是 onclick、onactivate、onmousedown、onmouseup、onmouseover、onmousemove、onmouseout、onload、onresize、 onunload 和 onrepeat。
- 5、svg
  svg 所定义的片段， 不管在独立的 svg 文件中，还是嵌入 html 中，都拥有独立的视口和坐标系统。
  svg 元素有如下专有属性:
  version：指明 SVG 文档遵循规范， 值为 1.0 | 1.1
  x：起始 横坐标
  y：起始 纵坐标
  width：svg 元素的宽度
  height：svg 元素的高度
  preserveAspectRatio：是否强制进行统一缩放.
  viewBox：允许指定一个给定的一组图形伸展以适应特定的容器元素。
- 6、视口 viewbox
  viewBox 属性的值是一个包含 4 个参数的列表 min-x, min-y, width and height
  下面的不设置 viewbox 的情形:
  矩形起始于 svg 的（0,0）位置，长度为 100px，宽度为 150px
  当设置 viewbo 后
  可以看到，不仅变大了，而且还向右下角 平移了。
  为什么呢？
- 我们可以看到 viewBox 的值为"-5 -5 100 150"，（-5 -5）分别表示 最小的 x 和最小的 y，也就是说，左上角原本的（0,0）现在等价于(-5,-5), 那么如果想要时 rect 还是在原来的左上角，那么才可以，而如果设置为
  就会向右向下平移 5px。
- 然后我们看到 viewbox 后两个是 100 150，分别表示 svg 的宽度和高度，即 100 等同于 svg 定义的宽度 500,150 等同于 svg 定义的高度 600，那么在 svg 容器里面的 rect 上设置 width 为 100，等同于 500，高为 150，等同于 600，这样会有个问题，就是本来 rect 的长宽比为 100:150，而通过 viewbox 设置后，长宽比会变化， 所以 svg 默认 即使 viewbox 宽度比变化了，但是 viewbox 容器里的元素的长宽比不会变化，会以缩放较小的那个为准。
  所以我们可以看到 viewbox 相当于 一个比例尺，对原本的 svg 的位置和宽高 做了相同比例的缩放。
  一共有下面的 5 个元素可以设置 viewbox:
  `<marker>, <pattern>, <svg>, <symbol>, 和 <view>` ###总结
- 1、svg 遵循 XML 文档格式，所以所以我们能给其定义 css、但同时要注意其每个图形独特的属性。
- 2、内置图形在同一位置的时候，默认的是空间向上重叠，可用 z-index 来理解，后来者居上。
- 3、我们能用 js 获取 SVG 的 DOM，并对其进行相关操作。
- 4、定义和分组这两个概念，入手相对较难理解，但对使用 D3.js 开发的时候很有帮助。定义能减少我们的工作量，提高代码复用率。分组方便我们统一管理 SVG 中的内置图形。
- 5、SVG 内置图形都有自己的坐标属性，正是这些属性，我们才能对内置图形做位置变化。
- 6、动画，SVG 有自己的动画定义，但是个人不建议使用，因为我们完全可以使用 CSS 的动画来实现同样的功能。
