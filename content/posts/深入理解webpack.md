---
title: "深入理解webpack"
date: 2022-04-20T21:36:43+08:00
draft: false
---

# 深入理解webpack

<!-- TOC -->

- [深入理解webpack](#深入理解webpack)
  - [从功能上理解webpack](#从功能上理解webpack)
    - [webpack两大功能](#webpack两大功能)
    - [webpack核心思路](#webpack核心思路)
  - [从架构上理解webpack](#从架构上理解webpack)
  - [webpack loader 和 plugin 的区别](#webpack-loader-和-plugin-的区别)
    - [webpack loader](#webpack-loader)
    - [webpack plugin](#webpack-plugin)

<!-- /TOC -->
 
## 从功能上理解webpack

> 本质上，webpack 是一个用于现代 JavaScript 应用程序的 静态模块打包工具。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容。

### webpack两大功能
1. 转译：
   1. 部分浏览器无法使用import/export需要进行转译，进行语法转译
   2. 将非js的其他资源转译为js代码，以供引入
2. 打包：
   1. 即使浏览器支持import/export语法，为避免js逐层引用，导致请求过多，可以通过依赖分析将多个文件进行打包。
   2. 同时可以将非js的其他资源均视为模块一并打包。

### webpack核心思路
1. 从入口文件开始分析依赖，将每个文件都总结成一个对象：
```js
{
    key:""//标识模块，可以是文件在项目下的路径
    deps:["",""]//依赖的模块
    code:function(require, module, module.exports)//等价于modules[key]即一个模块对象用于存放要到处的内容
}
```
   1. 其中code是一个按照commonJS约定定义的可执行的函数，代码的转译将原es6代码，转译为符合commonJS2的es5模块代码，所以code执行时也需要按照commonJS2规范，提供相应的接口。
2. 定义modules，记录并缓存所有模块，就是一个包含所有已加载对象的对象
3. 执行入口函数execute(deps[0].key)
    1. 作用：接收文件路径，然后执行code
4. 提供execute函数的定义
    1. 定义require，传给code执行，require就是执行execute，对依赖进行递归运行（运行）
    2. 定义modules[key]={}，传给code，在code运行过程中，将需要导出的内容，放在该对象上，该对象就是模块。

这里仅展示依赖分析代码，webpack核心功能完整代码详见[链接](https://github.com/Howardyangyixuan/my-webpack-demo-2)
```js
import babel = require('@babel/core');
import * as fs from 'fs';
import {dirname, relative, resolve} from 'path';

const module = {};
//项目绝对路径
const projectRoot = resolve(__dirname, 'project_3');
//依赖分析结果的类型
type DepRelation = { [key: string]: { deps: string[], code: string } }
//实例化一个分析结果对象
const depRelation: DepRelation = {};

//入口文件名
const filename = 'index.js';
//入口文件绝对路径
const filepath = resolve(projectRoot, filename);
collect(filepath);
console.log(depRelation);
console.log('done');

//获取path相对于项目目录的相对路径
function getProjectPath(path: string) {
  return relative(projectRoot, path);
}

//依赖分析
function collect(filepath: string) {
  //入口文件相对路径
  const key = getProjectPath(filepath);
  if (Object.keys(module).includes(key)) {
    console.warn('存在循环依赖! 依赖为:', key);
    return;
  }
  module[key] = undefined;
  const code = fs.readFileSync(filepath).toString();
  depRelation[key] = {deps: [], code};
  const ast = babel.parse(code, {sourceType: 'module'});
  babel.traverse(ast, {
    enter: item => {
      if (item.node.type === 'ImportDeclaration') {
        //依赖文件的绝对路径 = 入口文件所在文件夹绝对路径 + 依赖文件相对于入口文件的相对路径
        const depAbsolutePath = resolve(dirname(filepath), item.node.source.value);
        //依赖文件相对于项目目录的相对路径
        const depProjectPath = getProjectPath(depAbsolutePath);
        //将依赖存入依赖分析结果
        depRelation[key].deps.push(depProjectPath);
        //递归分析依赖
        collect(depAbsolutePath);
      }
    }
  });
}
```

## 从架构上理解webpack

从架构上，webpack基于Tapable事件发布与订阅框架，注册了一系列打包中的事件，webpack官方实现的的打包和转译发生在compile阶段。而这一架构使得webpack一方面可以通过拓展loader，在compile过程中支持各类资源打包，另一方面页可以通过实现plugin而极大拓展webpack的功能。

在梳理webpack源码过程中，整理出的主要事件以及对应进行的操作，整体思路可以理解，但细节仍有待深究，5个阶段的划分依据
```text
1. 初始化参数阶段
environment
afterEnvironment
- new JavascriptParsers()
initialize:
- compiler.run()
2. 开始编译准备阶段
beforeRun
run
- compiler.compile()
beforeCompile
3. 模块编译阶段，parse/traverse并使用loader
compile:
- new Compilation(params)
compilation
make:
- 借助 acorn 对源代码进行了 parse
- runLoaders()
finishMake: compilation.finish()
finishModules
4. 完成编译阶段，进行优化，如treeShaking等
seal
afterOptimizeTree
optimizeChunkModules:
- module.codeGeneration()
processAssets
afterSeal
afterCompile
5. 输出文件阶段：生成文件
shouldEmit
emit:创建文件
- emitAssets()
- emitFiles()
done
```

## webpack loader 和 plugin 的区别
### webpack loader
webpack自带打包器只支持js文件，当需要打包其他类型文件时，需要使用loader，loader本质就是将文件内容包装成可以运行的js代码。例如，css-loader是将css变成为js字符串，这样就可以配合style-loader将css放入style标签并加载到页面中。

### webpack plugin
如从架构上理解webpack中所述，webpack在其生命周期会触发各种事件，plugin通过监听这些事件，进行操作，而改变webpack的行为。

如最常使用的clean-webpack-plugin，通过监听emit和done事件，在emit即生成新的文件之前，清空原dist文件夹，在done事件之后删除assets之外的文件，保持一个干净的编译打包结果。

<hr>
从复习Cookie相关知识延伸过来，感觉收获很大，理解也更深入了，很棒继续加油！