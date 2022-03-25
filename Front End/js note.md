[TOC]

## 变量

## 原型和原型链

## 作用域和闭包

## 异步

## Web API

#### DOM 操作

#### BOM 操作

#### 事件绑定

#### ajax

#### 存储

###### `cookie`

- 本身用于浏览器和 server 通讯
- 被“借用”到本地存储来
- 前端可以用`document.cookie = '...'`来修改

缺点：

> - 存储大小，最大 4KB
> - http 请求时需要发送到服务端，增加数据请求量
> - 只能用`document.cookie = '...'`来修改（也可以用一些封装好的库操作）

###### `localStorage`和`sessionStorage`

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
