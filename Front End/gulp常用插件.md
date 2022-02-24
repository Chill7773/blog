[TOC]

`gulp`是一款前端自动化构建工具， 能自动化地完成 `javascript/html/image/css` 等文件的合并、压缩、检查、监听、测试等任务。

一些 MPA(多页面应用)开发和一些不够完善的小程序框架中使用可以极大提高效率。

本文将对一些常用的插件进行简单介绍：

## `gulp-rename` 文件重命名

功能：可以对一些文件进行重命名，详见[NPM 文档](https://www.npmjs.com/package/gulp-rename)

示例用法:

```js
const { src, dest } = require("gulp");
const rename = require("gulp-rename");
function defaultTask() {
  // 将src下的index.js，重命名为vender.js，输入到./dist文件夹下。
  return src(["src/index.js"]).pipe(rename("vendor.js")).pipe(dest("./dist"));
}
exports.default = defaultTask;
```

## `gulp-concat` 合并文件

功能：可以进行文件的连接，详见[NPM 文档](https://www.npmjs.com/package/gulp-concat)

示例用法:

```js
const { src, dest } = require("gulp");
const concat = require("gulp-concat");
function defaultTask() {
  // 将src下的所有js文件，连接为all.js，输入到./dist文件夹下。
  return src(["src/*.js"]).pipe(concat("all.js")).pipe(dest("./dist"));
}

exports.default = defaultTask;
```

## `gulp-sass` 编译 SCSS

功能：可以将`sass/scss`文件转为`css`，详见[NPM 文档](https://www.npmjs.com/package/gulp-sass)

示例用法：

```js
const { src, dest } = require("gulp");
const sass = require("gulp-sass")(require("sass"));
function defaultTask() {
  // 将src下的index.scss文件，转换成index.css，输入到./dist文件夹下。
  return src(["src/index.scss"])
    .pipe(sass().on("error", sass.logError))
    .pipe(dest("./dist"));
}
exports.default = defaultTask;
```

## `gulp-babel` 编译 js

功能：使用 babel 将新的`js`语法转换为旧的、可兼容的`js`语法，详见[NPM 文档](https://www.npmjs.com/package/gulp-babel)

示例用法：

```js
const { src, dest } = require("gulp");
const babel = require("gulp-babel");

// 将src下的index.js文件，使用@babel/preset-env转码规则进行转码
function defaultTask() {
  return src(["src/index.js"])
    .pipe(
      babel({
        presets: ["@babel/preset-env"],
      })
    )
    .pipe(dest("./dist"));
}

exports.default = defaultTask;
```

<font style="background-color:yellow;">注意：</font>需要`yarn add @babel/core @babel/preset-env --dev`来安装 core 模块和转码规则集

## `gulp-uglify` 压缩 js

功能：压缩`js`文件，详见[NPM 文档](https://www.npmjs.com/package/gulp-uglify)

示例用法：

```js
const { src, dest } = require("gulp");
const rename = require("gulp-rename");
const uglify = require("gulp-uglify");
// 将src下的index.js文件，压缩，并重命名为index.min.js
function defaultTask() {
  return src(["src/index.js"])
    .pipe(uglify())
    .pipe(rename("index.min.js"))
    .pipe(dest("./dist"));
}

exports.default = defaultTask;
```

## `gulp-htmlmin` 压缩 html

功能：压缩`HTML`文件，详见[NPM 文档](https://www.npmjs.com/package/gulp-htmlmin)

示例用法：

```js
const { src, dest } = require("gulp");
const htmlmin = require("gulp-htmlmin");
// 将src下的index.html文件，压缩，并输出到dist文件夹下
function defaultTask() {
  return src(["src/index.html"])
    .pipe(htmlmin({ collapseWhitespace: true }))
    .pipe(dest("./dist"));
}
exports.default = defaultTask;
```

## `gulp-clean-css` 压缩 css

功能：压缩`CSS`文件，详见[NPM 文档](https://www.npmjs.com/package/gulp-clean-css)

示例用法：

```js
const { src, dest } = require("gulp");
const cleanCSS = require("gulp-clean-css");
// 将src下的index.css文件，压缩，并输出到dist文件夹下
function defaultTask() {
  return src(["src/index.css"]).pipe(cleanCSS()).pipe(dest("./dist"));
}

exports.default = defaultTask;
```

## `gulp-imagemin` 压缩图片

功能：压缩` PNG, JPEG, GIF, SVG`图片，详见[NPM 文档](https://www.npmjs.com/package/gulp-imagemin)

示例用法：

```js
import gulp from "gulp";
import imagemin from "gulp-imagemin";
//  将src/images文件下的所有图片压缩，输出到dist/images下
export default () =>
  gulp.src("src/images/*").pipe(imagemin()).pipe(gulp.dest("./dist/images"));
```

<font style="background-color:yellow;">注意：</font>需要在支持`ESM`模块的文件下操作，比如`gulpfile.mjs`文件中，具体见[文档](https://gulpjs.com/docs/en/getting-started/javascript-and-gulpfiles)

## `gulp-connect`网页实时刷新

功能：构建简单的服务器并实现自动刷新，详见[NPM 文档](https://www.npmjs.com/package/gulp-connect)

示例用法：

```js
const { src, watch } = require("gulp");
const connect = require("gulp-connect");

// 监视 src 下js、css、html文件，如有变动实时刷新
watch(["src/*.js", "src/*.css", "src/*.html"], (cb) => {
  src("src/index.html").pipe(connect.reload());
  cb();
});

// 在文件夹src下创建端口为8888的web服务
function defaultTask(cb) {
  connect.server({
    root: "./src",
    port: 8888,
    livereload: true,
  });
  cb();
}
exports.default = defaultTask;
```
