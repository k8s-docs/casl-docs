---
title: 安装
categories: [guide]
weight: 1
meta:
  keywords: ~
  description: ~
---

## 需求

CASL 是同构的，所以可以在浏览器和 Nodejs 环境中使用。
它需要 ES5 兼容环境和 ES6 的“Map”类，这意味着最低支持的 ie 版本是 IE11，最低的 Nodejs 版本是 8.x。
但我们强烈建议使用最新的 Node.js 环境和浏览器，因为他们的 JS VM 工作得更快。

此外，' @casl/vue '和' @casl/aurelia '使用[ES6 WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)，所以它需要 polyfill 或更新版本的浏览器。

## 语义版本控制

CASL 在其所有官方项目中遵循[语义版本管理](https://semver.org/)。
可以在[发布说明](https://github.com/stalniy/casl/releases)或每个包中的' CHANGELOG.md '文件中找到更改。

## 官方软件包及其版本

| Project          | Status                                             | Description                           |
| ---------------- | -------------------------------------------------- | ------------------------------------- |
| [@casl/ability]  | [![@casl/ability-status]][@casl/ability-package]   | CASL's core package                   |
| [@casl/mongoose] | [![@casl/mongoose-status]][@casl/mongoose-package] | integration with [Mongoose][mongoose] |
| [@casl/prisma]   | [![@casl/prisma-status]][@casl/prisma-package]     | integration with [Prisma][prisma]     |
| [@casl/angular]  | [![@casl/angular-status]][@casl/angular-package]   | integration with [Angular][angular]   |
| [@casl/react]    | [![@casl/react-status]][@casl/react-package]       | integration with [React][react]       |
| [@casl/vue]      | [![@casl/vue-status]][@casl/vue-package]           | integration with [Vue][vue]           |
| [@casl/aurelia]  | [![@casl/aurelia-status]][@casl/aurelia-package]   | integration with [Aurelia][aurelia]   |

[@casl/ability]: ../intro
[@casl/mongoose]: ../../package/casl-mongoose
[@casl/prisma]: ../../package/casl-prisma
[@casl/angular]: ../../package/casl-angular
[@casl/react]: ../../package/casl-react
[@casl/vue]: ../../package/casl-vue
[@casl/aurelia]: ../../package/casl-aurelia
[@casl/ability-status]: https://img.shields.io/npm/v/@casl/ability.svg
[@casl/mongoose-status]: https://img.shields.io/npm/v/@casl/mongoose.svg
[@casl/prisma-status]: https://img.shields.io/npm/v/@casl/prisma.svg
[@casl/angular-status]: https://img.shields.io/npm/v/@casl/angular.svg
[@casl/react-status]: https://img.shields.io/npm/v/@casl/react.svg
[@casl/vue-status]: https://img.shields.io/npm/v/@casl/vue.svg
[@casl/aurelia-status]: https://img.shields.io/npm/v/@casl/aurelia.svg
[@casl/ability-package]: https://www.npmjs.com/package/@casl/ability
[@casl/mongoose-package]: https://www.npmjs.com/package/@casl/mongoose
[@casl/prisma-package]: https://www.npmjs.com/package/@casl/prisma
[@casl/angular-package]: https://www.npmjs.com/package/@casl/angular
[@casl/react-package]: https://www.npmjs.com/package/@casl/react
[@casl/vue-package]: https://www.npmjs.com/package/@casl/vue
[@casl/aurelia-package]: https://www.npmjs.com/package/@casl/aurelia
[mongoose]: http://mongoosejs.com/
[vue]: https://vuejs.org
[angular]: https://angular.io/
[react]: https://reactjs.org/
[aurelia]: http://aurelia.io
[prisma]: https://www.prisma.io/

## 第三方包

我们也可以在我们的项目中使用第三方包。
要找到它们，只需[使用“casl”关键字在 NPM 上搜索](https://www.npmjs.com/search?q=keywords:casl)

> 在[package.json#keywords](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#keywords)数组中添加“casl”，以显示基于 casl 的包在该列表中

## NPM, YARN, PNPM

通过包管理器安装是推荐的 CASL 安装方式。
要安装最新的稳定版本，请运行:

```sh
npm install @casl/ability
# or
yarn add @casl/ability
# or
pnpm add @casl/ability
```

## CDN

为了进行原型设计或学习，你可以使用最新的版本:

```html
<script src="https://cdn.jsdelivr.net/npm/@casl/ability"></script>
```

对于产品，我们建议链接到一个特定的版本号，以避免来自新版本的意外破坏:

```html
<script src="https://cdn.jsdelivr.net/npm/@casl/ability@5.1.0"></script>
```

记住，CASL 依赖于[@ucast/mongo2js]，而[@ucast/core]， [@ucast/js]和[@ucast/mongo]，这就是为什么你需要在' @casl/ability '之前指定所有这些库:

```html
<script src="https://cdn.jsdelivr.net/npm/@ucast/core"></script>
<script src="https://cdn.jsdelivr.net/npm/@ucast/mongo"></script>
<script src="https://cdn.jsdelivr.net/npm/@ucast/js"></script>
<script src="https://cdn.jsdelivr.net/npm/@ucast/mongo2js"></script>
<script src="https://cdn.jsdelivr.net/npm/@casl/ability"></script>
```

[@ucast/core]: https://www.npmjs.com/package/@ucast/core
[@ucast/js]: https://www.npmjs.com/package/@ucast/js
[@ucast/mongo]: https://www.npmjs.com/package/@ucast/mongo
[@ucast/mongo2js]: https://www.npmjs.com/package/@ucast/mongo

## 下载和使用 &lt;script&gt; 标签

只需[从 CDN 下载 CASL](https://cdn.jsdelivr.net/npm/@casl/ability)并包含一个脚本标记。
' casl '将被注册为一个全局变量。

> 希望你知道自己在做什么

## 不同构建的解释

In the `dist/` [directory of the NPM package](https://cdn.jsdelivr.net/npm/@casl/ability/dist/) you will find many different builds of CASL.js.
Here’s an overview of the difference between them:

| Build                                                                                                                                   | Description                                      |
| --------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| es6m/index.mjs                                                                                                                          | minified ES6 code with ES modules support.       |
| Intended for bundlers (e.g., [rollup], [webpack]) to create bundle for modern browsers or modern Nodejs version that support ES modules |
| es6c/index.js                                                                                                                           | minified ES6 code with Commonjs modules support. |
| Intended for modern nodejs environments that support ES6 but doesn't support ES modules                                                 |
| es5m/index.js                                                                                                                           | minified ES5 code with ES modules support.       |
| Should be used by bundlers (e.g., [rollup], [webpack]) to tree shake the module and skip babel's transpile process                      |
| umd/index.js                                                                                                                            | **deprecated**, minified ES5 code with UMD.      |
| Intended for AMD apps and simple prototypes in a browser                                                                                |
| types                                                                                                                                   | contains [typescript] type declaration files     |

[rollup]: https://rollupjs.org/guide/en/
[webpack]: https://webpack.js.org/
[typescript]: http://www.typescriptlang.org/

All official packages has the same directory layout (except of `@casl/mongoose` which does not have es5m version and packages that integrate CASL with UI frameworks which don't have es6c version).

## Webpack

Webpack 在严格的模式下运行 ESM JavaScript。
问题是 webpack4 只读取' package.json '的' main '属性，而忽略' resolve.mainFields '中的其他所有内容。
所有 casl 相关的包，包括 ucast，在' main '属性中分发 CommonJS 文件。
这就导致了下一个问题

> ERROR in ./node_modules/@casl/ability/dist/es6m/index.mjs
> Can't import the named export 'xxx' from non EcmaScript module (only default export is available)

The best way to fix it is to upgrade to webpack 5 which reads `exports` property of package.json when import packages and properly and works with ESM imports.
But if you stuck with webpack 4, read [this comment](https://github.com/stalniy/casl/issues/427#issuecomment-757539486) for possible workarounds.

## CSP 环境

一些环境，如谷歌 Chrome 应用程序，强制内容安全策略(CSP)，禁止使用“new Function()”来计算表达式。

CASL 不使用任何被禁止的函数，因此可以在任何环境中使用它。

## 开发构建

**Important**: GitHub ' /dist '文件夹中的构建文件在发布期间不会签入。要从 GitHub 上最新的源代码中使用 CASL，您必须自己构建它!导航你的项目根目录并运行:

```sh
git clone git@github.com:stalniy/casl.git
cd casl
pnpm i -r
cd packages/casl-ability
npm run build
```
