# 如何借助 Babel+TS+ESLint 构建现代 JS 工程环境？

## 代码如下

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
/******/ (() => { // webpackBootstrap
var __webpack_exports__ = {};
/*!**********************!*\
  !*** ./src/index.ts ***!
  \**********************/
var say = function say(statements) {
  console.log(statements);
};

// @ts-ignore

say("Tecvan");
/******/ })()
;
```

同时会有 eslint 报错。

## 整体流程复述

1. webpack 读取 config 文件，获取入口、出口、模式信息，加载 loader，对入口文件进行匹配，满足条件的执行对应 loader

2. webpack 配置中可以直接配置 babel 和 eslint 的使用（`babel-loader` 和 `eslint-webpack-plugin`），我们可以在 `.babelrc` 配置具体的转换配置，在 `.eslintrc` 配置具体的 lint 规则

## 几个问题

### 为什么不使用 `ts-loader` ?

1. `babel-loader` 会忽略 `ts` 类型检查，能让整个转换操作变得更快

2. `ts-loader` 用于将 `typescript` 转换为 `javascript`；`babel-loader` 则用于根据我们的要求将该 `javascript` 转换为目标浏览器友好的代码版本。

### ESLint、TypeScript、Babel 三种工具都分别提供了独立 CLI 形态的使用方法，为何还需要被接入到 Webpack 工作流程中？这种做法有什么收益？

1. 通过 webpack 管理控制项目代码的整个构建编译流程，简化多个工具独立操作的工作

2. 通过 webpack 进行编译流程控制，传递 module 工具的编译结果，及时终止编译出错的编译流程

3. 通过 webpack 控制根据配置规则 rule 对指定类型文件进行使用特定工具进行编译操作
