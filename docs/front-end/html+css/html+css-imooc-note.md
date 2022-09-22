[TOC]

### 如何理解 HTML 语义化

- 增加代码可读性
- 让搜索引擎更容易读懂（SEO）

### 块状元素 & 内联元素

- 块状元素：`display:block/table;` 例如：div h1-5 table ul ol p 等。
- 内联元素：`display:inline/inline-block;` 例如：span img input button 等。

### 盒模型宽度计算

- offsetWidth = (内容宽度 + 内边距 + 边框) ，无外边距。
- 在`box-sizing:border-box;`下，offsetWidth = 内容宽度。

### margin 纵向重叠问题

- 相邻元素的`margin-top` 和 `margin-bottom` 会发生重叠
- 空白内容的`<p></p>`也会重叠。

### margin 负值问题

- `margin-top`和`margin-left`负值，元素向上、向左移动。
- `margin-right`负值，右侧元素左移，自身不受影响。
- `margin-bottom`负值，下方元素上移，自身不受影响。

### BFC 理解与应用

- Block format context，块级格式化上下文
- 一块独立渲染区域，内部元素的渲染不会影响边界以外的元素。

##### 形成 BFC 的常见条件

- float 不是 none
- position 是 absolute 或 fixed
- overflow 不是 visible
- display 是 inline-block flex 的直接子元素。

##### BFC 常见应用

- 清除浮动
- 垂直方向的两个相邻元素的外边距叠加问题

### float 布局

##### 圣杯布局和双飞翼布局

###### 目的：

- 三栏布局，中间一栏最先加载和渲染(内容最重要)
- 两侧内容固定，中间内容随着宽度自适应
- 一般用于 PC 网页

###### 技术总结：

- 使用 float 布局
- 两侧使用 margin 负值，以便和中间内容横向重叠
- 防止中间内容被两侧覆盖，一个用 padding（圣杯） 、一个用 margin（双飞翼）

###### 代码：

- 圣杯布局：

  ```html
  <html>
    <head>
      <meta charset="UTF-8" />
      <title>圣杯布局</title>
      <style type="text/css">
        body {
          min-width: 550px;
        }
        #header {
          text-align: center;
          background-color: #f1f1f1;
        }
        #container {
          /* 布局代码 */
          padding-left: 200px;
          padding-right: 150px;
        }
        #center {
          background-color: #ccc;
          width: 100%;
          /* 布局代码 */
          float: left;
        }
        #left {
          background-color: yellow;
          width: 200px;
          /* 布局代码 */
          float: left;
          margin-left: -100%;
          position: relative;
          right: 200px;
        }
        #right {
          background-color: red;
          width: 150px;
          /* 布局代码 */
          float: left;
          margin-right: -150px;
        }
        #footer {
          text-align: center;
          background-color: #f1f1f1;
        }
        /* 手写 clearfix */
        .clearfix:after {
          content: "";
          display: table;
          clear: both;
        }
      </style>
    </head>
    <body>
      <div id="header">this is header</div>
      <div id="container" class="clearfix">
        <div id="center">this is center</div>
        <div id="left">this is left</div>
        <div id="right">this is right</div>
      </div>
      <div id="footer">this is footer</div>
    </body>
  </html>
  ```

- 双飞翼布局

  ```html
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="UTF-8" />
      <title>圣杯布局</title>
      <style type="text/css">
        body {
          min-width: 550px;
        }
        #header {
          text-align: center;
          background-color: #f1f1f1;
        }
        #center {
          background-color: #ccc;
          width: 100%;
          /* 布局代码 */
          float: left;
        }
        #center .inside {
          margin: 0 200px;
        }
        #left {
          background-color: yellow;
          width: 200px;
          /* 布局代码 */
          float: left;
          margin-left: -100%;
        }
        #right {
          background-color: red;
          width: 200px;
          /* 布局代码 */
          float: left;
          margin-left: -200px;
        }
        #footer {
          text-align: center;
          background-color: #f1f1f1;
        }
        /* 手写 clearfix */
        .clearfix:after {
          content: "";
          display: table;
          clear: both;
        }
      </style>
    </head>
    <body>
      <div id="header">this is header</div>
      <div class="clearfix">
        <div id="center">
          <div class="inside">this is center</div>
        </div>
        <div id="left">this is left</div>
        <div id="right">this is right</div>
      </div>
      <div id="footer">this is footer</div>
    </body>
  </html>
  ```

##### 手写 clearfix

```css
.clearfix:after {
  content: "";
  display: table;
  clear: both;
}
.claerfix {
  *zoom: 1; /* 兼容IE低版本 */
}
```

### flex 布局

相关内容见阮一峰[Flex 布局教程：实例篇](https://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

### absolute 和 relative 定位

- relative 依据自身定位
- absolute 依据最近一层的定位元素定位

### 居中对齐实现方式

##### 水平居中

- inline 元素：`text-align: center`
- block 元素：`margin: auto`
- absolute 元素：`left: 50%` + `margin-left负值`

##### 垂直居中

- inline 元素：line-height 的值等于 height 值
- absolute 元素：`top: 50%` + `margin-top负值`
- absolute 元素：`transform(-50%, -50%)`
- absolute 元素：`top, left, bottom, right = 0` + `margin: auto`

### line-height 如何继承

- 写具体数值，如 30px，则继承该值
- 写比例，如 2.5 ，则继承该比例
- 写百分比，如 200% ，则继承计算出来的值

### rem 是什么

rem 是一个长度单位

- px，绝对长度单位，最常用
- em，相对长度单位,相对于父元素，不常用
- rem， 相对长度单位,相对于根元素，常用于响应式布局

### 响应式布局的常用方案

- media-query ，根据不同的屏幕宽度设置根元素 font-size。
- vh & vw 方案
