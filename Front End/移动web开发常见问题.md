[TOC]

## 对于移动端元素点击态的处理

在移动端 web 开发的时候，为了提高用户体验，通常会给被触控的元素加上一个点击态效果来对用户的操作进行反馈。

为了方便大多数开发者会选择伪类`:active`来实现，但是这种方法存在比较大的局限性：

> - 仅当轻触时有效。
> - 长按时会产生动画闪动。
> - 无法实现点击时触发动画，离开时取消动画的效果。

所以为了实现更自然，美观的点击态效果，我们需要利用[`touch`事件](https://developer.mozilla.org/zh-CN/docs/Web/API/Touch_events)来实现我们的需求。

下面是在`react`中的代码演示：

```jsx
/* 略 */

// 功能是在点击文字内容为："登录", "取消", "注册"的时候实现点击态效果
// 开始之前我们需要在css文件中实现.active的样式
useEffect(() => {
  const arr = ["登录", "取消", "注册"];
  // 触摸开始添加active类名
  const touchstart = function (e: any) {
    const { target } = e;
    if (arr.includes(target.innerHTML)) {
      target.classList.add("active");
    }
  };
  document.body.addEventListener("touchstart", touchstart);

  // 在移动出目标元素范围时移除active类名
  const touchmove = function (e: any) {
    const { target } = e;
    const rect = target.getBoundingClientRect();
    if (arr.includes(target.innerHTML)) {
      // 移出元素时，取消active状态
      if (
        e.changedTouches[0].pageX < rect.left ||
        e.changedTouches[0].pageX > rect.right ||
        e.changedTouches[0].pageY < rect.top ||
        e.changedTouches[0].pageY > rect.bottom
      ) {
        target.classList.remove("active");
      }
    }
  };
  document.body.addEventListener("touchmove", touchmove);

  // 在触摸取消时移除active类名
  const touchcancel = function (e: any) {
    const { target } = e;
    if (arr.includes(target.innerHTML)) {
      target.classList.remove("active");
    }
  };
  document.body.addEventListener("touchcancel", touchcancel);

  // 在触摸结束时移除active类名
  const touchend = function (e: any) {
    const { target } = e;
    if (arr.includes(target.innerHTML)) {
      target.classList.remove("active");
    }
  };
  document.body.addEventListener("touchend", touchend);

  return () => {
    // 组件卸载时 移除事件监听
    document.body.removeEventListener("touchstart", touchstart);
    document.body.removeEventListener("touchmove", touchmove);
    document.body.removeEventListener("touchend", touchend);
    document.body.removeEventListener("touchcancel", touchcancel);
  };
});

/* 略 */
```

## 移动端调试工具

## 响应式解决方案

## 常用开源 UI 组件库

## meta 使用总结

- H5 页面窗口自动调整到设备宽度，并禁止用户缩放页面

```html
<meta
  name="viewport"
  content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no"
/>
```

- 忽略将页面中的数字识别为电话号码

```html
<meta name="format-detection" content="telephone=no" />
```

- 忽略 Android 平台中对邮箱地址的识别

```html
<meta name="format-detection" content="email=no" />
```

- 当网站添加到主屏幕快速启动方式，可隐藏地址栏，仅针对 ios 的 safari

```html
<!-- ios7.0版本以后，safari上已看不到效果 -->
<meta name="apple-mobile-web-app-capable" content="yes" />
```

- 将网站添加到主屏幕快速启动方式，仅针对 ios 的 safari 顶端状态条的样式

```html
<!-- 可选default、black、black-translucent -->
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
```
