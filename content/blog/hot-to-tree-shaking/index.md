---
title: 前端项目如何配置tree-shaking
date: 2019-11-28
description: 前端项目如何配置tree-shaking
---

[tree-shaking](https://developer.mozilla.org/zh-CN/docs/Glossary/Tree_shaking)是 rollup 提出的一个概念，指在 build 的时候去除没有被引用的代码，减小 bundle 代码体积。它依赖于 es2015 模块语法的静态结构特性。

到目前为止，前端项目使用 tree-shaking 的案例还不多。一方面是使用 tree-shking 对于代码书写方式、build 流程
都需要做特殊配置。另一方面是由于 tree-shaking 会删除没有被使用到的代码，但是有副作用的代码，虽然没有引用到的地方，也不应该被删除，这一步也需要额外的配置。

这里不解释什么是 tree-shaking 以及实现原理，只说明如何书写代码以及如何配置项目可以达到 tree-shaking 的目的。

## 代码书写规范

1. 必须使用 es6 的 module

```js
example.js
export function foo() {}
export function bar() {}

import { foo } from "./example.js" //   ✅
const { foo } = require("./example.js") //   ❎
```

2. 不要整体导入，只导入必要的模块

```js
import { get } from "lodash" //   ✅
import _ from "lodash" //   ❎
```

## 打包流程配置

1. babel 配置不转换 module

```
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "modules": false
      }
    ]
  ]
}
```

2. webpack 配置 tree-shaking 相关设置
```
{
  mode: 'production'
}
或者：
{
  optimization: {
   usedExports: true,
 }
}
```
3. package.json 指明副作用位置
```
...
"sideEffects": [
  "**/*.css",
  "**/*.scss",
  "./esnext/index.js",
  "./esnext/configure.js"
],
...
```
