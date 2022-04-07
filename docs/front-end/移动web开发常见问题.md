[TOC]

## 对于移动端元素点击态的处理

在移动端 web 开发的时候，为了提高用户体验，通常会给被触控的元素加上一个点击态效果来对用户的操作进行反馈。

为了方便大多数开发者会选择伪类`:active`来实现，但是这种方法存在比较大的局限性：

> - 仅当轻触时有效。
> - 长按时会产生动画闪动。
> - 无法实现点击时触发动画，离开时取消动画的效果。

所以为了实现更自然，美观的点击态效果，我们需要利用[`touch`事件](https://developer.mozilla.org/zh-CN/docs/Web/API/Touch_events)来实现我们的需求。

下面是在`react`中的代码演示：

```tsx
// 开始之前我们需要在css文件中实现.active的样式
import React from "react";
const index: React.FC = () => {
  // --------------- 实现按钮点击效果 ---------------
  // 触摸开始添加active类名
  const touchStart = function (e: React.TouchEvent<HTMLDivElement>) {
    const target = e.target as Element;
    target.classList!.add("active");
  };
  // 在移动出目标元素范围时移除active类名
  const touchMove = function (e: React.TouchEvent<HTMLDivElement>) {
    const target = e.target as Element;
    const rect = target.getBoundingClientRect();
    if (
      e.changedTouches[0].pageX < rect.left ||
      e.changedTouches[0].pageX > rect.right ||
      e.changedTouches[0].pageY < rect.top ||
      e.changedTouches[0].pageY > rect.bottom
    ) {
      target.classList.remove("active");
    }
  };
  // 在触摸取消和触摸结束时移除active类名
  const touchEndOrCancel = function (e: React.TouchEvent<HTMLDivElement>) {
    const target = e.target as Element;
    target.classList.remove("active");
  };
  return (
    <>
      <div
        onTouchStart={touchStart}
        onTouchMove={touchMove}
        onTouchCancel={touchEndOrCancel}
        onTouchEnd={touchEndOrCancel}
      >
        开锁
      </div>
    </>
  );
};

export default index;
```

## 移动端调试工具

- **(个人推荐)** [Eruda](https://github.com/liriliri/eruda)
- [vConsole](https://github.com/Tencent/vConsole)
- [spy-debugger](https://github.com/wuchangming/spy-debugger)

## 响应式解决方案

- `media-query` ，根据不同的屏幕宽度设置根元素 font-size。

- **(推荐)** 使用 `viewport` 单位 (vw, vh, vmin, vmax)

  - 推荐[postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport) _（一款 PostCSS 插件，用于将 px 单位转化为 vw/vh 单位。）_

- `rem` 布局

  - [postcss-pxtorem](https://github.com/cuth/postcss-pxtorem)（一款 PostCSS 插件，用于将 px 单位转化为 rem 单位）
  - [lib-flexible](https://github.com/amfe/lib-flexible) _（用于设置 rem 基准值，官方已放弃维护，推荐 viewport 方案）_

## 常用开源 UI 组件库

- [Vant](https://youzan.github.io/vant/#/zh-CN/home) (Vue)
- [Ant Design Mobile](https://mobile.ant.design/zh)(React)

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
