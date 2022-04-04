[TOC]

## 变量类型

#### `typeof` 运算符

- 识别所有值类型
- 识别函数
- 识别是否是引用类型（不可再细分）

#### 手写深拷贝

```js
function deepClone(obj = {}) {
  if (typeof obj !== "object" || obj == null) {
    return obj;
  }
  let result;
  if (obj instanceof Array) {
    result = [];
  } else {
    result = {};
  }
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      result[key] = deepClone(obj[key]);
    }
  }
  return result;
}
```

#### 类型转换相关问题

- 字符串拼接

```js
console.log(100 + 10); // 110
console.log(100 + "10"); // 10010
console.log(false + "10"); // false10
```

- ==运算符

```js
console.log(100 == "100"); // true
console.log(0 == ""); // true
console.log(0 == false); // true
console.log(false == ""); // true
console.log(null == undefined); // true
```

- if 语句和逻辑运算

```js
// 以下是falsely变量。除此之外都是truly变量
// if语句和逻辑判断中进行falsely变量和truly变量的判断
!!0 === false;
!!NaN == false;
!!"" == false;
!!null == false;
!!undefined == false;
!!false == false;
```

## 原型和原型链

#### 原型关系

- 每个 class 都有显示原型 `prototype`
- 每个实例都有隐式原型`__proto__`
- 实例的`__proto__`指向对应 class 的`prototype`

> 注意： 获取属性和方法时，先在自身属性和方法寻找，找不到自动去`__proto__`中查找

#### 原型链

代码：

```js
class People {
  /* 略*/
}
class Student extends People {
  /* 略*/
}
const xialuo = new Student();
```

对应的原型链：

<img src="/images/front-end/2022-04-03-prototype-chain.png" width="750px"/>

#### 手写 jQuery

```js
class jQuery {
  constructor(selector) {
    const result = document.querySelectorAll(selector);
    const length = result.length;
    for (let i = 0; i < length; i++) {
      this[i] = result[i];
    }
    this.length = length;
    this.selector = selector;
  }
  get(index) {
    return this[index];
  }
  each(fn) {
    for (let i = 0; i < this.length; i++) {
      const elem = this[i];
      fn(elem);
    }
  }
  on(type, fn) {
    return this.each((elem) => {
      elem.addEventListener(type, fn, false);
    });
  }
  // 扩展很多 DOM API
}

// 插件
jQuery.prototype.dialog = function (info) {
  alert(info);
};

// “造轮子”
class myJQuery extends jQuery {
  constructor(selector) {
    super(selector);
  }
  // 扩展自己的方法
  addClass(className) {}
  style(data) {}
}
```

## 作用域和闭包

#### 什么是作用域

- 全局作用域
- 函数作用域
- 块级作用域

#### 自由变量

- 一个变量在当前作用域没有定义，但被使用了
- 向上级作用域，一层一层依次寻找，直到找到为止
- 如果在全局作用域都没有找到，则报错 `xx is undefined`

#### 闭包

- 作用域应用的特殊情况，有两种表现：
- 函数作为参数被传递
- 函数作为返回值被返回

> 注意：所有的自由变量的查找，是在函数定义的地方，向上级作用域查找，不是在执行的地方！！！

#### this

1. 使用场景

> - 作为普通函数，返回 window 对象
> - 使用 call、apply、bind
> - 作为对象方法被调用
> - 在 class 方法被调用
> - 箭头函数

2.  示例代码：

```js
function fn() {
  console.log(this);
}
fn(); // this: window
fn.call({ a: 1 }); // this: {a:1}

const obj = {
  fn() {
    setTimeout(function () {
      console.log(this); // this: window
    });
  },
  anfn() {
    setTimeout(() => {
      console.log(this); // this: 当前对象
    });
  },
};

class Obj {
  constructor(name) {
    this.name = name;
  }
  fn() {
    console.log(this);
  }
}
const instance = new Obj("name");
instance.fn(); // this: instance对象
```

> 注意：this 的取值是在函数执行的时候确认

#### 手写 bind

```js
Function.prototype.bind1 = function () {
  // 将参数拆解为数组
  const args = Array.from(arguments);

  // 获取 this（数组第一项）
  const t = args.shift();

  // fn1.bind(...) 中的 fn1
  const self = this;

  // 返回一个函数
  return function () {
    return self.apply(t, args);
  };
};
```

## 异步

####

## Web API

### DOM 操作

#### DOM 是哪种数据结构

- 树（DOM 树）

#### DOM 操作常用 API

- DOM 节点操作：获取节点；操作 `attribute` 和 `property`
- DOM 结构操作：新增/插入节点；获取子元素/父元素；删除子元素

#### attribute 和 property 的区别

- property：修改对象属性，不会体现到 html 结构中
- attribute：修改 html 属性，会修改 html 结构
- 两者都有可能引起 DOM 重新渲染

#### 一次性插入多个节点，考虑性能

- 使用 `document.createDocumentFragment()`
- 考虑缓存 DOM 查询

### BOM 操作

知识点：`navigator`; `screen`; `location`; `history`;

#### 如何识别浏览器类型

- 使用`navigator.userAgent`

#### 分析拆解 url 各个部分

```js
console.log(location.href);
console.log(location.protocol);
console.log(location.pathname);
console.log(location.search);
console.log(location.hash);
```

### DOM 事件

知识点：事件代理；事件冒泡；事件绑定；

#### 手写通用的事件监听函数

```js
function bindEvent(elem, type, selector, fn) {
  if (fn == null) {
    fn = selector;
    selector = null;
  }
  elem.addEventListener(type, (event) => {
    const target = event.target;
    if (selector) {
      // 代理绑定
      if (target.matches(selector)) {
        fn.call(target, event);
      }
    } else {
      // 普通绑定
      fn.call(target, event);
    }
  });
}

// 普通绑定
const btn1 = document.getElementById("btn1");
bindEvent(btn1, "click", function (event) {
  // console.log(event.target) // 获取触发的元素
  event.preventDefault(); // 阻止默认行为
  alert(this.innerHTML);
});

// 代理绑定
const div3 = document.getElementById("div3");
bindEvent(div3, "click", "a", function (event) {
  event.preventDefault();
  alert(this.innerHTML);
});
```

#### 事件冒泡流程

- 基于 DOM 树形结构
- 事件会顺着触发元素向上冒泡
- 应用场景：事件代理

#### 无限下拉图片列表，如何监听每个图片点击

- 事件代理
- 用`e.target` 获取触发元素
- 用`matches`来判断是否是触发元素

### ajax

#### 手写简易`ajax`

```js
function ajax(url) {
  const p = new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url, true);
    xhr.onreadystatechange = function () {
      // 0 - UNSET 尚未调用open方法
      // 1 - OPENED open方法已经被调用
      // 2 - HEADERS_RECEIVED send 方法已被调用，header已被接收
      // 3 - LOADING 下载中，responseText已有部分内容
      // 4 - DONE 下载完成
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          resolve(JSON.parse(xhr.responseText));
        } else if (xhr.status === 404 || xhr.status === 500) {
          reject(new Error("404 not found"));
        }
      }
    };
    xhr.send(null);
  });
  return p;
}
```

#### 跨域的常用实现方式

1. 同源策略

> - ajax 请求时，浏览器要求当前网页和 server 必须同源
> - 同源：协议、域名、端口，三者必须一致
> - 加载图片（可用于第三方服务统计打点） js（JSONP、CDN） css（CDN） 可无视同源策略
> - 所有的跨域必须经过 server 端的允许和配合

2. JSONP

> - 服务器可以任意拼接数据返回，只要符合 html 格式要求
> - 同理与 `<script src=""></script>`，其可以绕过跨域
> - 所以`<script>`可以获取跨域的数据，只要服务端愿意

3. CORS

> - 服务器端设置 http header

### 存储

#### `cookie`

- 本身用于浏览器和 server 通讯
- 被“借用”到本地存储来
- 前端可以用`document.cookie = '...'`来修改

缺点：

> - 存储大小，最大 4KB
> - http 请求时需要发送到服务端，增加数据请求量
> - 只能用`document.cookie = '...'`来修改（也可以用一些封装好的库操作）

#### `localStorage`和`sessionStorage`

- HTML5 专门为存储而设计，最大可存 5M
- API 简单易用 `setItem` 、`getItem`
- 不会随着 http 请求被发送出去

二者区别：

> - localStorage 数据会永久存储，除非代码或手动删除
> - sessionStorage 数据只存在于当前会话，浏览器关闭则清空
> - 一般用 localStorage 会更多一些

与`cookie`区别：

> - 容量
> - API 易用性
> - 是否跟随 http 请求发送出去

## http

#### http 状态码

1. 分类

> - 1xx 服务器收到请求
> - 2xx 请求成功
> - 3xx 重定向
> - 4xx 客户端错误
> - 5xx 服务端错误

2. 常见状态码

> - 200 成功
> - 301 永久重定向（配合 location，浏览器自动处理）
> - 302 临时重定向（配合 location，浏览器自动处理）
> - 304 资源未被修改
> - 404 资源未找到
> - 403 没有权限
> - 500 服务器错误
> - 504 网关超时

#### http methods

1. 传统的 methods

> - get 获取服务端数据
> - post 向服务器提交数据

2.  现在的 methods

> - get 获取数据
> - post 新建数据
> - patch/put 更新数据
> - delete 删除数据

3.  Restful API

> - 一种 API 设计方法
> - 传统 API 设计：把每个 url 当做一个功能
> - Restful API 设计：把每个 url 当做一个唯一的资源：
> - 尽量不使用 url 参数
> - 使用 method 表示操作类型

#### http headers

1. 常见的 Response Headers：

> - Content-type 返回数据的格式，如`application/json`
> - Content-length 返回数据的大小，多少字节
> - Content-Ecoding 返回数据的压缩算法，如 gzip
> - Set-Cookie 服务端修改 cookie
> - 缓存相关：`Cache-Control`, `Expires`, `Last-Modified`, `If-Modified-Since`, `Etag`, `If-None-Match`

2. 常见的 Request Headers：

> - Accept 浏览器可接收的数据格式
> - Accept-Ecoding 浏览器可接收的压缩算法，如：gzip
> - Accept-Language 浏览器可接收的语言，如：zh-CN
> - `Connection:keep-alive` 一次 TCP 连接重复使用
> - cookie
> - Host
> - User-Agent（UA）浏览器信息
> - Content-type 发送数据的格式，如`application/json`

#### http 缓存

1. http 缓存策略 - 强制缓存

> - `Cache-Control`控制强制缓存的逻辑：`max-age`,`no-cache`,`no-store`,`public`,`private`
> - `Expires`控制缓存过期（已被`Cache-Control`代替）

2. http 缓存策略 - 协商缓存

> - 服务端缓存策略
> - 服务器判断客户端资源，是否和服务器资源一样
> - 一致则返回 304，否则返回 200 和最新资源
> - 资源标识：`Last-Modified`-资源的最后修改时间，`Etag`-资源的唯一标识 ，共存优先使用`Etag`

<img src="/images/front-end/2022-04-04-http-cache.png" width="750px"/>

3. 刷新操作对缓存的影响

> - 三种刷新操作：1. 正常操作：地址栏输入、前进后退和链接跳转等；2.手动刷新：`F5`；3. 强制刷新：`crtl + F5`；
> - 正常操作：强制缓存有效、协商缓存有效
> - 手动刷新：强制缓存失效、协商缓存有效
> - 强制刷新：强制缓存失效、协商缓存失效

#### https

1. http 和 https

> - http 是明文传输，容易被劫持
> - https = http + 加密，劫持了也无法解密

2. 加密方式

<img src="/images/front-end/2022-04-04-https.png" width="750px"/>

## 运行环境

#### 从输入 url 到渲染出页面的整个过程

- 下载资源：各个资源类型下载
- 渲染页面：结合 html css js 图片等

#### window.onload 和 DOMContentLoaded 的区别

- window.onload：资源全部加载完成才能执行，包括图片
- DOMContentLoaded：DOM 渲染完成即可，图片可能尚未下载

#### 性能优化

1. 原则：

> - 多使用内存、缓存其他方法
> - 减少 CPU 计算量，减少网络加载耗时

2. 让加载更快

> - 减少资源体积：压缩代码
> - 减少访问次数：合并代码，SSR，缓存
> - 使用更快的网络：CDN

3. 让渲染更快

> - CSS 放在 head，JS 放在 body 最下面
> - 尽早开始执行 JS，用 DOMContentLoaded 触发
> - 懒加载
> - 对 DOM 查询进行缓存
> - 频繁的 DOM 操作，合并到一起插入 DOM 结构
> - 节流 throttle；防抖 debounce

#### 手写防抖 debounce

```js
// 防抖
function debounce(fn, delay = 500) {
  // timer 是闭包中的
  let timer = null;

  return function () {
    if (timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      fn.apply(this, arguments);
      timer = null;
    }, delay);
  };
}
```

#### 手写节流 throttle

```js
// 节流
function throttle(fn, delay = 100) {
  let timer = null;

  return function () {
    if (timer) {
      return;
    }
    timer = setTimeout(() => {
      fn.apply(this, arguments);
      timer = null;
    }, delay);
  };
}
```

#### 预防 XSS 跨站请求攻击

- 替换特殊字符: 如`<`变为`&lt;`，`>`变为`&gt;`

#### 预防 XSRF 跨站请求伪造

- 使用 post 接口
- 增加验证：密码，指纹，短信验证码等

## Others
