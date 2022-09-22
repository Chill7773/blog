[TOC]

## webpack 基本使用

#### 拆分配置和 merge

- 创建三个文件：`webpack.common.js`, `webpack.dev.js`, `webpack.prod.js` 分别对应公共配置，开发环境配，生产环境配置
- 安装`webpack-merge`, 使用`const { merge } = require('webpack-merge')` 进行合并。
- 示例代码：

`webpack.common.js`：

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { srcPath, distPath } = require("./paths");

module.exports = {
  entry: path.join(srcPath, "index"),
  plugins: [
    new HtmlWebpackPlugin({
      template: path.join(srcPath, "index.html"),
      filename: "index.html",
    }),
  ],
};
```

`webpack.dev.js`：

```js
/* 略 */
const webpackCommonConf = require("./webpack.common.js");
const { merge } = require("webpack-merge");

module.exports = merge(webpackCommonConf, {
  mode: "development",
  devServer: {
    port: 8080,
    historyApiFallback: true, // 任何请求都会返回index.html文件，这只能用于只有一个 HTML 文件的应用。
    hot: true, // 启用 webpack 的热模块替换特性
    progress: true, // 显示打包的进度条
    contentBase: distPath, // 根目录
    open: true, // 自动打开浏览器
    compress: true, // 启动 gzip 压缩
    // 设置代理 —— 如果有需要的话！
    proxy: {
      // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
      "/api": "http://localhost:3000",

      // 将本地 /api2/xxx 代理到 localhost:3000/xxx
      "/api2": {
        target: "http://localhost:3000",
        pathRewrite: {
          "/api2": "",
        },
      },
    },
  },
  plugins: [
    new webpack.DefinePlugin({
      // window.ENV = 'production'
      ENV: JSON.stringify("development"),
    }),
  ],
});
```

`webpack.prod.js`：

```js
/* 略 */
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const webpackCommonConf = require("./webpack.common.js");
const { merge } = require("webpack-merge");

module.exports = merge(webpackCommonConf, {
  mode: "production",
  output: {
    filename: "bundle.[contenthash:8].js", // 打包代码时，加上 hash 戳
    path: distPath,
    // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
  },
  plugins: [
    new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
    new webpack.DefinePlugin({
      // window.ENV = 'production'
      ENV: JSON.stringify("production"),
    }),
  ],
});
```

#### 启动本地服务

- 安装`webpack-dev-server` 模块
- 配置`devServer`选项（在上述示例代码中有展示）
- 在`package.json` 中配置：

```json
{
  "scripts": {
    "dev-without-dev-server": "webpack --config build/webpack.dev.js",
    // webpack5
    "dev": "webpack serve --config build/webpack.dev.js",
    // webpack4
    "dev": "webpack-dev-server --config build/webpack.dev.js"
  }
}
```

#### 处理 es6

- 新建`.babelrc`：

```json
{
  "presets": ["@babel/preset-env"],
  "plugins": []
}
```

- 安装`@babel/core`, `@babel/preset-env`, `babel-loader`

- 配置 webpack 配置文件：

```js
/* 略 */
module.exports = {
  /* 略 */
  module: {
    rules: [
      {
        test: /\.js$/,
        use: ["babel-loader"],
        include: srcPath,
        exclude: /node_modules/,
      },
    ],
  },
};
```

#### 处理样式

- 安装相关`loader`

- 配置`postcss.config.js`, 安装`autoprefixer`(增加 css 前缀)

```js
module.exports = {
  plugins: [require("autoprefixer")],
};
```

- 配置 webpack 配置文件：

```js
/* 略 */
module.exports = {
  /* 略 */
  module: {
    rules: [
      {
        test: /\.css$/,
        // loader 的执行顺序是：从后往前
        use: ["style-loader", "css-loader", "postcss-loader"], // 加了 postcss
      },
      {
        test: /\.less$/,
        // 增加 'less-loader' ，注意顺序
        use: ["style-loader", "css-loader", "less-loader"],
      },
    ],
  },
};
```

#### 处理图片

- 安装相关`loader`

- dev 环境下：

```js
module.exports = {
  /* 略 */
  module: {
    rules: [
      // 直接引入图片 url
      {
        test: /\.(png|jpg|jpeg|gif)$/,
        use: "file-loader",
      },
    ],
  },
};
```

- prod 环境下：

```js
module.exports = {
  /* 略 */
  module: {
    rules: [
      // 图片 - 考虑 base64 编码的情况
      {
        test: /\.(png|jpg|jpeg|gif)$/,
        use: {
          loader: "url-loader",
          options: {
            // 小于 5kb 的图片用 base64 格式产出
            // 否则，依然延用 file-loader 的形式，产出 url 格式
            limit: 5 * 1024,

            // 打包到 img 目录下
            outputPath: "/img1/",

            // 设置图片的 cdn 地址（也可以统一在外面的 output 中设置，那将作用于所有静态资源）
            // publicPath: 'http://cdn.abc.com'
          },
        },
      },
    ],
  },
};
```

## webpack 高级特性

## webpack 性能优化

## babel 使用和配置
