[TOC]

# 前端设计规范

该规范的设计基于`prettier`、`stylelint`、`eslint`这三种常见的代码质量管理工具。

为了使得规范在开发时生效，我们需要在编辑器中安装上述三种工具的相关插件。

## `prettier`规范配置

1. `npm i prettier --save-dev`安装依赖

2. 新建`.prettierrc.js`文件，复制以下配置

```js
module.exports = {
  // 超过最大值换行
  printWidth: 100,
  // 缩进字节数
  tabWidth: 2,
  // 使用制表符而不是空格缩进行
  useTabs: true,
  // 结尾不用分号(true有，false没有)
  semi: true,
  // 使用单引号(true单双引号，false双引号)
  singleQuote: true,
  // 更改引用对象属性的时间 可选值"<as-needed|consistent|preserve>"
  quoteProps: "as-needed",
  // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
  bracketSpacing: true,
  // 多行时尽可能打印尾随逗号。（例如，单行数组永远不会出现逗号结尾。） 可选值"<none|es5|all>"，默认none
  trailingComma: "all",
  // 在JSX中使用单引号而不是双引号
  jsxSingleQuote: false,
  //  (x) => {} 箭头函数参数只有一个时是否要有小括号。avoid：省略括号 ,always：不省略括号
  arrowParens: "avoid",
  // 如果文件顶部已经有一个 doclock，这个选项将新建一行注释，并打上@format标记。
  insertPragma: false,
  // 指定要使用的解析器，不需要写文件开头的 @prettier
  requirePragma: false,
  // 默认值。因为使用了一些折行敏感型的渲染器（如GitHub comment）而按照markdown文本样式进行折行
  proseWrap: "preserve",
  // 在html中空格是否是敏感的 "css" - 遵守CSS显示属性的默认值， "strict" - 空格被认为是敏感的 ，"ignore" - 空格被认为是不敏感的
  htmlWhitespaceSensitivity: "css",
  // 换行符使用 lf 结尾是 可选值"<auto|lf|crlf|cr>"
  endOfLine: "lf",
  // 这两个选项可用于格式化以给定字符偏移量（分别包括和不包括）开始和结束的代码
  rangeStart: 0,
  rangeEnd: Infinity,
};
```

## `stylelint`规范配置

1. `npm i stylelint --save-dev`安装依赖

2. 安装相关其他扩展 - `npm i stylelint-config-prettier stylelint-config-recess-order stylelint-config-standard stylelint-less stylelint-scss --save-dev`

   > `stylelint-config-prettier` - 用于关掉所有不必要的或者可能与`prettier`冲突的规则
   > `stylelint-config-recess-order` - 用于排序 css 属性
   > `stylelint-config-standard ` - 用于打开一些公共约定规则
   > `stylelint-less` - 用于支持 less（项目中未使用 less 可以不安装）
   > `stylelint-scss` - 用于支持 scss（项目中未使用 scss 可以不安装）

3. 新建`stylelintrc.js`，复制以下配置

```js
module.exports = {
  extends: [
    "stylelint-config-standard", // 配置stylelint拓展插件
    "stylelint-config-prettier", // 配置stylelint和prettier兼容
    "stylelint-config-recess-order", // 配置stylelint css属性书写顺序插件,
  ],
  plugins: ["stylelint-less", "stylelint-scss"], // 配置less scss拓展插件
  rules: {
    indentation: null, // 指定缩进空格
    "no-descending-specificity": null, // 禁止在具有较高优先级的选择器后出现被其覆盖的较低优先级的选择器
    "function-url-quotes": "always", // 要求或禁止 URL 的引号 "always(必须加上引号)"|"never(没有引号)"
    "string-quotes": "double", // 指定字符串使用单引号或双引号
    "unit-case": null, // 指定单位的大小写 "lower(全小写)"|"upper(全大写)"
    "color-hex-case": "lower", // 指定 16 进制颜色的大小写 "lower(全小写)"|"upper(全大写)"
    "color-hex-length": "long", // 指定 16 进制颜色的简写或扩写 "short(16进制简写)"|"long(16进制扩写)"
    "rule-empty-line-before": "never", // 要求或禁止在规则之前的空行 "always(规则之前必须始终有一个空行)"|"never(规则前绝不能有空行)"|"always-multi-line(多行规则之前必须始终有一个空行)"|"never-multi-line(多行规则之前绝不能有空行。)"
    "font-family-no-missing-generic-family-keyword": null, // 禁止在字体族名称列表中缺少通用字体族关键字
    "block-opening-brace-space-before": "always", // 要求在块的开大括号之前必须有一个空格或不能有空白符 "always(大括号前必须始终有一个空格)"|"never(左大括号之前绝不能有空格)"|"always-single-line(在单行块中的左大括号之前必须始终有一个空格)"|"never-single-line(在单行块中的左大括号之前绝不能有空格)"|"always-multi-line(在多行块中，左大括号之前必须始终有一个空格)"|"never-multi-line(多行块中的左大括号之前绝不能有空格)"
    "property-no-unknown": null, // 禁止未知的属性(true 为不允许)
    "no-empty-source": null, // 禁止空源码
    "declaration-block-trailing-semicolon": null, // 要求或不允许在声明块中使用尾随分号 string："always(必须始终有一个尾随分号)"|"never(不得有尾随分号)"
    "selector-class-pattern": null, // 强制选择器类名的格式
    "value-no-vendor-prefix": null, // 关闭 vendor-prefix(为了解决多行省略 -webkit-box)
    "at-rule-no-unknown": null,
    "selector-pseudo-class-no-unknown": [
      true,
      {
        ignorePseudoClasses: ["global", "v-deep", "deep"],
      },
    ],
  },
};
```

## `eslint`规范配置

### `React + JS`规范配置

### `React + TS`规范配置

### `umi`框架中的规范配置

## 其他规范

### promise 的使用，避免回调地狱

- 对于有两个 promise: `p1`,`p2`

```js
const p1 = new Promise((resolve) => {
  /** 略 */
  resolve(2);
});
const p2 = new Promise((resolve) => {
  /** 略 */
  resolve(3);
});
```

- 我们需要在`p1`resolve 后,执行`p2`，然后将拿到值进行处理，我们应该**避免**使用下面的形式：

```js
p1.then((p1_val) => {
  p2.then((p2_val) => {
    console.log(p1_val * p2_val);
  });
});
```

- 上面的写法法极易造成回调地狱，造成代码的可读性极差，难以维护。建议使用下面的写法：

```js
/** 方法1 Promise.all */
Promise.all([p1, p2]).then(([p1_val, p2_val]) => {
  console.log(p1_val * p2_val);
});

/** 方法2 链式调用 */
const fn = (d) => {
  return p2.then((res) => res * d);
};
p1.then(fn).then((res) => {
  console.log(res);
});

/** 方法3 async/await方案 推荐 */
const fn = async () => {
  const p1_val = await p1;
  const p2_val = await p2;
  console.log(p1_val * p2_val);
};
fn();
```

### 使用 jsDoc 注释

- 在我们的前端项目中，我们应该尽可能使用 jsDoc 去注释代码，比如：

```js
/**
 * @file 用于在审核驳回后，进行路由选择的相关操作
 */

/**
 * 设置路由列表到内存
 * @param {string[]} list 对应的路由列表
 * @returns {boolean} 表示设置成功与否
 */
export const setRouterList = (list) => {
  if (!Array.isArray(list)) return false;
  sessionStorage.setItem(ROUTER_LIST, JSON.stringify(list || []));
  return true;
};
```

- 上面示例代码中`@file`表示了文件用途；方法`setRouterList`的注释说明了该函数的功能，参数，返回值。并且在一些编辑器中可以悬浮查看该函数的注释，无须跳转到定义处了解。[了解更多关于 jsDoc 的用法](https://www.shouce.ren/api/view/a/13232)

### 避免在`window`上挂载全局变量

- 我们应该避免在`window`对象上挂载全局变量和方法，以防止我们的项目中出现过多莫名的变量和方法，使得项目难以阅读和维护。
- 最好是将其封装在一个文件里`export`出来。
- 若我们不得不在`window`中挂载，最好先在`eslint`配置文件中配置`globals`，然后在使用前，用类似`const {caService} = window;`方法进行取值后再使用。

### 文件行数控制

- 对于单个`.js`,`.jsx`,`.ts`,`.tsx`文件，代码行数应控制在 300 行左右，不宜超过 500 行，以提高代码的可维护性和降低复杂度。

### 后缀名区分文件功能

- 对于一些**页面文件**、**组件文件**、或者**文件中使用了 jsx 语法**，我们应该将文件的后缀名改为`.jsx`或`.tsx`，用于加以区分文件功能，使得项目结构清晰。

## 项目目录结构
