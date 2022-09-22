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

#### event loop（事件循环/事件轮询）

1. 什么是 event loop

> - JS 是单线程运行的
> - 异步要基于回调来实现
> - event loop 就是异步回调的实现原理

2. DOM 事件和 event loop

> - 异步（setTimeout, ajax 等）使用回调，基于 event loop
> - DOM 事件也使用回调，基于 event loop

3. event loop 过程

> - 同步代码，一行一行放在 Call Stack 执行
> - 遇到异步，会先记录下，等待时机
> - 时机到了，移动到 Callback Queue
> - 如 Call Stack 为空（即同步代码执行完成），event loop 开始工作
> - 轮询查找 Callback Queue，如有则移动到 Call Back 执行
> - 然后继续轮询查找

#### Promise

1. 关于`then`和`catch`面试题

```js
// 第一题
Promise.resolve()
  .then(() => {
    console.log(1);
  })
  .catch(() => {
    console.log(2);
  })
  .then(() => {
    console.log(3);
  });
// Output:
// 1
// 3

// 第二题
Promise.resolve()
  .then(() => {
    // 返回 rejected 状态的 promise
    console.log(1);
    throw new Error("erro1");
  })
  .catch(() => {
    // 返回 resolved 状态的 promise
    console.log(2);
  })
  .then(() => {
    console.log(3);
  });
// Output:
// 1
// 2
// 3

// 第三题
Promise.resolve()
  .then(() => {
    // 返回 rejected 状态的 promise
    console.log(1);
    throw new Error("erro1");
  })
  .catch(() => {
    // 返回 resolved 状态的 promise
    console.log(2);
  })
  .catch(() => {
    console.log(3);
  });
// Output:
// 1
// 2
```

#### async - await

> - async/await 使用同步语法，消灭回调函数
> - 执行 async 函数，返回的是 Promise 对象
> - await 相当于 Promise 中的 then
> - try...catch 可捕获异常，代替 Promise 的 catch

#### 宏任务 macroTask 和微任务 microTask

1. 什么是宏任务和微任务

> - 宏任务：`setTimeout`,`setInterval`,`ajax`,DOM 事件
> - 微任务：`Promise`, `async/await`
> - 微任务执行时机比宏任务早

2. event loop 和 DOM 渲染

> - 每次 Call Stack 清空（即每次轮询结束），即同步任务执行完
> - 都是 DOM 重新渲染的机会，DOM 结构如有改变则重新渲染
> - 然后再去触发下一次 Event Loop

3. 微任务和宏任务区别

> - 宏任务：DOM 渲染后触发
> - 微任务：DOM 渲染前触发

#### 手写 Promise

```js
class MyPromise {
  state = "pending"; // 状态，'pending' 'fulfilled' 'rejected'
  value = undefined; // 成功后的值
  reason = undefined; // 失败后的值

  resolveCallbacks = []; // pending状态下，存储成功的回调
  rejectCallbacks = []; // pending状态下，存储失败的回调

  constructor(fn) {
    const resolveHandler = (value) => {
      if (this.state === "pending") {
        this.state = "fulfilled";
        this.value = value;
        this.resolveCallbacks.forEach((fn) => fn(this.value));
      }
    };
    const rejectHandler = (reason) => {
      if (this.state === "pending") {
        this.state = "rejected";
        this.reason = reason;
        this.rejectCallbacks.forEach((fn) => fn(this.reason));
      }
    };
    try {
      fn(resolveHandler, rejectHandler);
    } catch (error) {
      rejectHandler(error);
    }
  }

  then(fn1, fn2) {
    // 当pending状态下，fn1 fn2会被存储到 callbacks 中
    fn1 = typeof fn1 === "function" ? fn1 : (v) => v;
    fn2 = typeof fn2 === "function" ? fn2 : (v) => v;

    if (this.state === "pending") {
      return new MyPromise((resolve, reject) => {
        this.resolveCallbacks.push(() => {
          try {
            const newValue = fn1(this.value);
            resolve(newValue);
          } catch (error) {
            reject(error);
          }
        });

        this.rejectCallbacks.push(() => {
          try {
            const newReason = fn2(this.reason);
            reject(newReason);
          } catch (error) {
            reject(error);
          }
        });
      });
    }
    if (this.state === "fulfilled") {
      return new MyPromise((resolve, reject) => {
        try {
          const newValue = fn1(this.value);
          resolve(newValue);
        } catch (error) {
          reject(error);
        }
      });
    }
    if (this.state === "rejected") {
      return new MyPromise((resolve, reject) => {
        try {
          const newReason = fn2(this.reason);
          reject(newReason);
        } catch (error) {
          reject(error);
        }
      });
    }
  }

  catch(fn) {
    return this.then(undefined, fn);
  }
}

MyPromise.resolve = (value) => {
  return new MyPromise((resolve, reject) => resolve(value));
};

MyPromise.reject = (reason) => {
  return new MyPromise((resolve, reject) => reject(reason));
};

MyPromise.all = (promiseList = []) => {
  return new MyPromise((resolve, reject) => {
    const result = [];
    const length = promiseList.length;
    let resolvedCount = 0;
    promiseList.forEach((p) => {
      p.then((data) => {
        result.push(data);
        resolvedCount++;
        if (resolvedCount === length) {
          resolve(result);
        }
      }).catch((err) => reject(err));
    });
  });
};

MyPromise.race = (promiseList = []) => {
  let resolved = false;
  return new MyPromise((resolve, reject) => {
    promiseList.forEach((p) => {
      p.then((data) => {
        if (!resolved) {
          resolve(data);
          resolved = true;
        }
      }).catch((err) => reject(err));
    });
  });
};
```

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

#### var 和 let const 的区别

- var 是 ES5 语法，let const 是 ES6 语法；var 有变量提升
- var 和 let 是变量，可修改；const 是常量，不可修改；
- let const 有块级作用域，var 没有

#### typeof 能判断哪些类型

- undefined string number boolean symbol
- object （注意 typeof null === 'object'）
- function

#### 列举强制类型转换和隐式类型转换

- 强制：parseInt、parseFloat、toString 等
- 隐式：if、逻辑运算、==、+拼接字符串

#### 手写深度比较，模拟 lodash isEqual

```js
const isEqual = (obj1, obj2) => {
  const isObject = (obj) => typeof obj === "object" && obj !== null;

  if (!isObject(obj1) || !isObject(obj2)) return obj1 === obj2;
  if (obj1 === obj2) return true;
  if (Object.keys(obj1).length !== Object.keys(obj2).length) return false;

  for (let key in obj1) {
    const res = isEqual(obj1[key], obj2[key]);
    if (!res) return false;
  }
  return true;
};
```

#### 数组 slice 和 splice 区别

- 功能区别（slice - 切片，splice - 剪接）
- 参数返回值不同
- slice 是纯函数；splice 不是，有副作用

#### 闭包有何影响？

- 变量会常驻内存，得不到释放。闭包不要乱用

#### 函数声明和函数表达式的区别

- 函数声明：`function fn() {}`
- 函数表达式：`const fn = function() {}`
- 函数声明会在代码执行前预加载，而函数表达式不会

#### new Object() 和 Object.create()的区别

- `{}` 等同于 `new Object()`，原型 `Object.prototype`
- `Object.create(null)` 没有原型
- `Object.create({...})` 可指定原型

#### 手写字符串 trim

```js
String.prototype.trim1 = function () {
  return this.replace(/^\s+/, "").replace(/\s+$/, "");
};
```

#### 如何使用 JS 实现继承

- class 继承
- prototype 继承

#### 介绍 RAF requestAnimationFrame

- 想要动画流畅，更新频率要 60 帧/s，即 16.67ms 更新一次视图
- setTimeout 要手动控制频率，而 RAF 浏览器自动控制
- 后台标签或隐藏 iframe 中 ，RAF 会暂停，而 setTimeout 依然执行

```js
function animate() {
  /* 略 */
  window.requestAnimationFrame(animate); // animate 执行频率不用自己控制
}
animate();
```

#### Map 和 Object 区别

- API 不同，Map 可以以任意类型为 key
- Map 是有序结构
- Map 操作同样很快

#### Set 和数组的区别

- API 不同
- Set 元素不能重复
- Set 是无序结构

#### WeakMap 和 WeakSet

- 弱引用，防止内存泄漏
- WeakMap 只能用对象作为 key，WeakSet 只能用对象做 value
- 没有 forEach、size，只能用 add、delete、has
