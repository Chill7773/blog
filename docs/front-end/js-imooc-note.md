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

## Web API

### DOM 操作

### BOM 操作

### 事件绑定

### ajax

### 存储

##### `cookie`

- 本身用于浏览器和 server 通讯
- 被“借用”到本地存储来
- 前端可以用`document.cookie = '...'`来修改

缺点：

> - 存储大小，最大 4KB
> - http 请求时需要发送到服务端，增加数据请求量
> - 只能用`document.cookie = '...'`来修改（也可以用一些封装好的库操作）

##### `localStorage`和`sessionStorage`

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

## 运行环境

## Others
