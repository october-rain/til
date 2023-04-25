# webpack 搭建简单工程环境

## 如何借助 Babel+TS+ESLint 构建现代 JS 工程环境？

### 代码如下

安装依赖

```shell
npm i -D webpack webpack-cli \
    # babel 依赖
    @babel/core @babel/cli @babel/preset-env babel-loader \
    # TypeScript 依赖
    typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin \
    @babel/preset-typescript \
    # ESLint 依赖
    eslint eslint-webpack-plugin
```

`webpack.config.js` 文件如下：

```js
const path = require("path");

// eslint-webpack-plugin 插件配置 eslint
const ESLintPlugin = require("eslint-webpack-plugin");

module.exports = {
  // 入口文件
  entry: "./src/index.ts",
  // 不会进行代码压缩
  mode: "development",
  devtool: false,
  // 输出目录
  output: {
    filename: "[name].js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        // 只有正则命中才会应用此规则
        test: /\.ts$/,
        // 用于声明这条规则的 Loader 处理器序列，所有命中该规则的文件都会被传入 Loader 序列做转译处理
        use: [
          "babel-loader",
          // 如果不使用 options 字段，则默认读取 .babelrc 文件
          // {
          //   loader: "babel-loader",
          //   options: {
          //     presets: ["@babel/preset-env"],
          //   },
          // },
        ],
      },

      // babel-loader 和 ts-loader 选择一个就可以
      // {
      //   test: /\.ts$/,
      //   use: "ts-loader",
      // }
    ],
  },
  plugins: [new ESLintPlugin({ extensions: [".js", ".ts"] })],
};
```

`.eslintrc` 文件如下：（这个主要是 eslint 规则）

```json
{
  // "extends": "standard"
  "parser": "@typescript-eslint/parser",
  // 此处使用 @babel/preset-typescript 插件转译 TypeScript 代码。
  "plugins": ["@typescript-eslint"],
  "extends": ["plugin:@typescript-eslint/recommended"]
}
```

`.babelrc` 文件如下：

```json
{
  "presets": [
    // ts -> es6
    "@babel/preset-typescript",
    // es6 -> es5
    "@babel/preset-env"
  ]
}
```

入口文件代码如下：

```ts
const say = (statements: string) => {
  console.log(statements);
};

// @ts-ignore

say("Tecvan");
```

执行 `npx webpack` ，得到 dist 目录导出 index.js 文件如下：

```js
/******/ (() => {
  // webpackBootstrap
  var __webpack_exports__ = {};
  /*!**********************!*\
  !*** ./src/index.ts ***!
  \**********************/
  var say = function say(statements) {
    console.log(statements);
  };

  // @ts-ignore

  say("Tecvan");
  /******/
})();
```

同时会有 eslint 报错。

### 整体流程复述

1. webpack 读取 config 文件，获取入口、出口、模式信息，加载 loader，对入口文件进行匹配，满足条件的执行对应 loader

2. webpack 配置中可以直接配置 babel 和 eslint 的使用（`babel-loader` 和 `eslint-webpack-plugin`），我们可以在 `.babelrc` 配置具体的转换配置，在 `.eslintrc` 配置具体的 lint 规则

### 几个问题

#### 为什么不使用 `ts-loader` ?

1. `babel-loader` 会忽略 `ts` 类型检查，能让整个转换操作变得更快

2. `ts-loader` 用于将 `typescript` 转换为 `javascript`；`babel-loader` 则用于根据我们的要求将该 `javascript` 转换为目标浏览器友好的代码版本。

### ESLint、TypeScript、Babel 三种工具都分别提供了独立 CLI 形态的使用方法，为何还需要被接入到 Webpack 工作流程中？这种做法有什么收益？

1. 通过 webpack 管理控制项目代码的整个构建编译流程，简化多个工具独立操作的工作

2. 通过 webpack 进行编译流程控制，传递 module 工具的编译结果，及时终止编译出错的编译流程

3. 通过 webpack 控制根据配置规则 rule 对指定类型文件进行使用特定工具进行编译操作

## webpack 如何处理 css ？

### 代码参考

webpack 配置如下

```js
const path = require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const HTMLWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "./src/index",
  output: {
    filename: "[name].js",
    path: path.join(__dirname, "./dist"),
  },
  mode: "development",
  devtool: false,
  module: {
    rules: [
      {
        // 只有正则命中才会应用此规则
        test: /\.less$/i,
        include: {
          and: [path.join(__dirname, "./src/")],
        },
        // 用于声明这条规则的 Loader 处理器序列，所有命中该规则的文件都会被传入 Loader 序列做转译处理
        // ! 下面配置语义上相当于 style-loader(css-loader(css)) 链式调用，
        use: [
          // 根据运行环境判断使用那个 loader
          process.env.NODE_ENV === "development"
            ? "style-loader"
            : MiniCssExtractPlugin.loader,
          {
            loader: "css-loader",

            options: {
              importLoaders: 1,
            },
          },
          "postcss-loader",
          "less-loader",
        ],
      },
    ],
  },
  plugins: [new MiniCssExtractPlugin(), new HTMLWebpackPlugin()],
};
```

postcss 配置如下

```js
module.exports = {
  plugins: [require("autoprefixer")],
};
```

入口文件如下：

```js
import "./index.less";

const node = document.createElement("span");

node.textContent = "Hello world";

document.body.appendChild(node);
```

less 文件如下：

```less
// 变量
@size: 12px;
@color: #006633;

// 混合
.mx-bordered() {
  border: 1px solid #000;
}

// 嵌套
body {
  // 函数计算
  background: spin(lighten(@color, 25%), 8);
  font-weight: bold;
  padding: @size;

  .main {
    // 数学运算
    font-size: @size * 2;
    .mx-bordered;
    color: darken(@color, 10%);
    padding: @size * 0.6;
  }

  ::placeholder {
    color: gray;
  }
}
```

### 加载 css 的整体流程

几个插件的意义和区别：

#### 让 webpack 能够识别 css 的插件

- `css-loader`：该 Loader 会将 CSS 等价翻译为形如 `module.exports = "${css}"` 的 JavaScript 代码，使得 Webpack 能够如同处理 JS 代码一样解析 CSS 内容与资源依赖;
- `style-loader`：该 Loader 将在产物中注入一系列 runtime 代码，这些代码会将 CSS 内容注入到页面的 `<style>` 标签，使得样式生效；
- `mini-css-extract-plugin`：该插件会将 CSS 代码抽离到单独的 .css 文件，并将文件通过 `<link>` 标签方式插入到页面中。

> PS：当 Webpack 版本低于 5.0 时，请使用 extract-text-webpack-plugin 代替 mini-css-extract-plugin。

三种组件各司其职：`css-loader` 让 Webpack 能够正确理解 CSS 代码、分析资源依赖；`style-loader`、`mini-css-extract-plugin` 则通过适当方式将 CSS 插入到页面，对页面样式产生影响

#### 让 css 能够插入到 html 中的插件

- `html-webpack-plugin` 是将构建产物以 `link` 标签形式插入 `html` 的插件。需要和 `style-loader` 或者 `mini-css-extract-plugin` 结合使用。

> 执行 build 会自动创建 html，使用 serve 访问 dist 目录下的 html 即可

#### 在 webpack 中使用 css 预编译器

- `less-loader` 是将 less 语法转换为 css 的 loader。
- `sass-loader` 类似
- `stylus-loader` 类似

#### 使用 postcss

css 预处理器和 postcss 两者主要区别在于预处理器通常定义了一套 CSS 之上的超集语言；PostCSS 并没有定义一门新的语言，而是与 @babel/core 类似，只是实现了一套将 CSS 源码解析为 AST 结构，并传入 PostCSS 插件做处理的流程框架，具体功能都由插件实现。

> 预处理器之于 CSS，就像 TypeScript 与 JavaScript 的关系；而 PostCSS 之于 CSS，则更像 Babel 与 JavaScript。

- `postcss-loader`

下面是一些插件

- `autoprefixer`：基于 Can I Use 网站上的数据，自动添加浏览器前缀
- `postcss-preset-env`：一款将最新 CSS 语言特性转译为兼容性更佳的低版本代码的插件
- `postcss-less`：兼容 Less 语法的 PostCSS 插件，类似的还有：postcss-sass、poststylus
- `stylelint`：一个现代 CSS 代码风格检查器，能够帮助识别样式代码中的异常或风格问题

#### 加载顺序

```js
[
  process.env.NODE_ENV === "development"
    ? "style-loader"
    : MiniCssExtractPlugin.loader,
  "css-loader",
  "postcss-loader",
  'less-loader',
],
```

这几个 loader 是有加载顺序的。上述配置语义上相当于 `style-loader(css-loader(postcss-loader(less-loader(css))))` 链式调用

### 几个注意事项

#### style-loader 和 mini-css-extract-plugin 什么区别

1. `style-loader`，会在 js 主程序中注入 runningtime 代码，配合`HTMLWebpackPlugin`，动态以`style`标签注入样式，且支持`hmr`；`mini-css-extract-plugin`将 css 打包成独立的文件，不支持`hmr`；
2. 性能影响：`style-loader`由于混合在 js 中，代码的颗粒度很大，无论 js 或 css 变动会导致缓存失效，并且内嵌 css 不能异步加载，会导致页面卡顿，`mini-css-extract-plugin`因为是`link`引入所以不会影响，且没有注入多余的 js 代码。

#### importLoaders 参数是干什么呢？

`importLoaders`这个字段用于`css-loader`，当`css-loader`遇到`@import`语法时，也就是引入别的 css 模块时，如果这个字段什么都不设置，那么被引入的模块从`css-loader`开始应用，即使`css-loader`可能后面还存在`postcss-loader`，这当然不是我们期待的效果，我们希望所有的 css 代码都能被`postcss-loader`作用，从而帮助我们实现例如补充浏览器前缀等功能`(autoprefixer)`，所以需要设置这个字段，具体表现如下：

- 如果设置为 1，那么使用`@import`被引入的模块会从`css-loader`的后一个`loader`开始作用，然后是`css-loader`作用，然后是`css-loader`前面的 loader 作用，直至第一个 loader;
- 如果设置为 2，那么从`css-loader`的第二个 loader 开始作用，流程与设置为 1 时一样，不赘述。

> 💊💊💊：但请注意，以上情况只针对于 css 文件，如果项目中使用了样式预处理器 less sass 等，则完全不用加这个参数，因为`less-loader`或者`sass-loader`解析后生成的 css 代码内已经不存在`@import`语法，所有的`@import`语法全都被 loader 解析成对应的 css 模块代码了，等到 css-loader 处理样式代码的时候，没有`@import`，就完全不需要这个参数了。

## 搭建 react 环境

### 踩过的坑

#### 控制台报错 Uncaught ReferenceError: process is not defined

[Webpack: Bundle.js - Uncaught ReferenceError: process is not defined](https://stackoverflow.com/questions/41359504/webpack-bundle-js-uncaught-referenceerror-process-is-not-defined) 这个是真的有点坑，好像是 webpack5 新增的问题，解决办法如下：

```js
// webpack needs to be explicitly required
const webpack = require("webpack");
// import webpack from 'webpack' // (if you're using ESM)

module.exports = {
  /* ... rest of the config here ... */

  plugins: [
    // fix "process is not defined" error:
    new webpack.ProvidePlugin({
      process: "process/browser",
    }),
  ],
};
```

#### You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file.

如果要配置 react 的 开发环境，ts / js 文件中也可能会有 react 的 jsx 代码，比如：

```js
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./index.jsx";

const root = ReactDOM.createRoot(document.getElementById("app"));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

这种情况下，如果只匹配 jsx（如下），就会报该错误

```js
module: {
  rules: [
    {
      test: /(\.jsx)$/, // test: /(\.jsx|js)$/,
      loader: "babel-loader",
      exclude: /node_modules/,
    },
  ],
},
```
