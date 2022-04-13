[TOC]

## webpack ����ʹ��

#### ������ú� merge

- ���������ļ���`webpack.common.js`, `webpack.dev.js`, `webpack.prod.js` �ֱ��Ӧ�������ã����������䣬������������
- ��װ`webpack-merge`, ʹ��`const { merge } = require('webpack-merge')` ���кϲ���
- ʾ�����룺

`webpack.common.js`��

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

`webpack.dev.js`��

```js
/* �� */
const webpackCommonConf = require("./webpack.common.js");
const { merge } = require("webpack-merge");

module.exports = merge(webpackCommonConf, {
  mode: "development",
  devServer: {
    port: 8080,
    historyApiFallback: true, // �κ����󶼻᷵��index.html�ļ�����ֻ������ֻ��һ�� HTML �ļ���Ӧ�á�
    hot: true, // ���� webpack ����ģ���滻����
    progress: true, // ��ʾ����Ľ�����
    contentBase: distPath, // ��Ŀ¼
    open: true, // �Զ��������
    compress: true, // ���� gzip ѹ��
    // ���ô��� ���� �������Ҫ�Ļ���
    proxy: {
      // ������ /api/xxx ���� localhost:3000/api/xxx
      "/api": "http://localhost:3000",

      // ������ /api2/xxx ���� localhost:3000/xxx
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

`webpack.prod.js`��

```js
/* �� */
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const webpackCommonConf = require("./webpack.common.js");
const { merge } = require("webpack-merge");

module.exports = merge(webpackCommonConf, {
  mode: "production",
  output: {
    filename: "bundle.[contenthash:8].js", // �������ʱ������ hash ��
    path: distPath,
    // publicPath: 'http://cdn.abc.com'  // �޸����о�̬�ļ� url ��ǰ׺���� cdn ��������������ʱ�ò���
  },
  plugins: [
    new CleanWebpackPlugin(), // ��Ĭ����� output.path �ļ���
    new webpack.DefinePlugin({
      // window.ENV = 'production'
      ENV: JSON.stringify("production"),
    }),
  ],
});
```

#### �������ط���

- ��װ`webpack-dev-server` ģ��
- ����`devServer`ѡ�������ʾ����������չʾ��
- ��`package.json` �����ã�

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

#### ���� es6

- �½�`.babelrc`��

```json
{
  "presets": ["@babel/preset-env"],
  "plugins": []
}
```

- ��װ`@babel/core`, `@babel/preset-env`, `babel-loader`

- ���� webpack �����ļ���

```js
/* �� */
module.exports = {
  /* �� */
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

#### ������ʽ

- ��װ���`loader`

- ����`postcss.config.js`, ��װ`autoprefixer`(���� css ǰ׺)

```js
module.exports = {
  plugins: [require("autoprefixer")],
};
```

- ���� webpack �����ļ���

```js
/* �� */
module.exports = {
  /* �� */
  module: {
    rules: [
      {
        test: /\.css$/,
        // loader ��ִ��˳���ǣ��Ӻ���ǰ
        use: ["style-loader", "css-loader", "postcss-loader"], // ���� postcss
      },
      {
        test: /\.less$/,
        // ���� 'less-loader' ��ע��˳��
        use: ["style-loader", "css-loader", "less-loader"],
      },
    ],
  },
};
```

#### ����ͼƬ

- ��װ���`loader`

- dev �����£�

```js
module.exports = {
  /* �� */
  module: {
    rules: [
      // ֱ������ͼƬ url
      {
        test: /\.(png|jpg|jpeg|gif)$/,
        use: "file-loader",
      },
    ],
  },
};
```

- prod �����£�

```js
module.exports = {
  /* �� */
  module: {
    rules: [
      // ͼƬ - ���� base64 ��������
      {
        test: /\.(png|jpg|jpeg|gif)$/,
        use: {
          loader: "url-loader",
          options: {
            // С�� 5kb ��ͼƬ�� base64 ��ʽ����
            // ������Ȼ���� file-loader ����ʽ������ url ��ʽ
            limit: 5 * 1024,

            // ����� img Ŀ¼��
            outputPath: "/img1/",

            // ����ͼƬ�� cdn ��ַ��Ҳ����ͳһ������� output �����ã��ǽ����������о�̬��Դ��
            // publicPath: 'http://cdn.abc.com'
          },
        },
      },
    ],
  },
};
```

## webpack �߼�����

## webpack �����Ż�

## babel ʹ�ú�����
