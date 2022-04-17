---
title: "浅析Babel"
date: 2022-04-12T19:55:47+08:00
draft: false
---

# 浅析 Babel 

实例文中实例详见[本文完整代码](https://github.com/Howardyangyixuan/my-webpack-demo-1)
## 背景
由于不同浏览器实现的js各有不同，开发者为了使开发的应用在各个浏览器上都能够正常使用，产生了将开发时使用的js代码转换为更具兼容性的代码的需求。

而Babel就是为此而生的，Babel最主要的功能就是将es6代码转化为es5，使低版本浏览器兼容最新的es6语法。Babel是由ts和js实现的。


## Babel 工作流程

Babel的功能是将代码进行转译，因此Babel本质上就是一个编译器。 因此Babel的原理自然也逃不出编译的五个阶段。
1. 词法分析
2. 语法分析
3. 语义分析与中间代码产生
4. 代码优化
5. 目标代码生成

具体而言，Babel将整个编译流程简化为三大部分，并定义对应的API
1. @babel/parser：将原代码经过词法分析和语法分析，变为抽象语法树（AST）
   1. 背景：原名Babylon，基于acorn（一个基于JS的小而快的JS语法分析器）
	 2. 抽象语法树 Abstract Syntax Tree（AST）：用树形结构抽象表示源代码的语法结构，树上的每个节点（node）都表示源代码中的一种结构。
2. @babel/traverse：遍历AST上的节点，根据plugin和presets中的转换规则，进行增删改查操作
3. @babel/generator：将AST转换为目标代码

以下是一个将所有let声明转换为var声明的例子：
```js
import {parse} from '@babel/parser';
import traverse from '@babel/traverse';
import generate from '@babel/generator';

const code = `let a = 'let'; let b = 1`;
const ast = parse(code, {sourceType: 'module'});
traverse(ast, {
  enter: item => {
    if (item.node.type === 'VariableDeclaration') {
      if (item.node.kind === 'let') {
        item.node.kind = 'var';
      }
    }
  }
});
const result = generate(ast,{},code);
console.log(result.code);
```

## Babel 转换规则：Plugin 和 Presets
为了将代码进行转换，可以对比不同版本规范，像上述例子一样制定规则进行代码转译，官方提供的插件被称为plugin，如``@babel/plugin-transform-arrow-functions``用于转换箭头函数，为了方便的转换所有规则，官方提供了``@babel/preset-env``，这是一个预设的插件集合，这组插件包含了es6到es5的所有翻译规则，避免一个个手动引入插件。

```js
import * as babel from '@babel/core';
import * as fs from 'fs';

const code = fs.readFileSync('./test.js').toString()
const ast = babel.parse(code, {sourceType: 'module'});
const result = babel.transformFromAstSync(ast, code, {
  presets: ['@babel/preset-env']
});
fs.writeFileSync('./test.es5.js',result.code)
```
当然也可以通过配置文件babel.config.json/.babelrc.json/package.json进行配置，详见[文档](https://www.babeljs.cn/docs/configuration)，对于配置文件，一般情况直接复制粘贴，如有额外需求再自行定制，没必要背诵默写。

## @babel/preset-env 与 @babel/polyfill

然而并非使用了preset-env就万事大吉，虽然经过preset-env的语法规则转译，语法均符合规范，但对于新标准引入的各种函数（包括新的内置组件/静态方法/实例方法/生成器函数等）则仍需提供其具体实现才能够使代码正常运行。
```text
es5未实现的函数
1. 内置组件：如Promise和WeakMap
2. 静态方法：如Array.from和Object.assign
3. 实例方法：如Array.prototype.includes
4. 生成器函数(generator functions)的构造函数和方法
```
@babel/polyfill 模块包含两部分来模拟完整的 ES2015+ 环境
1. core-js/stable 用于模拟 ECMAScript 的功能 
2. regenerator-runtime/runtime 用于模拟生成器特性

> 从 Babel 7.4.0 版本开始，建议直接包含 core-js/stable 和 regenerator-runtime/runtime

但如果直接引入两个模块会造成两个问题
1. 因为直接引入 @babel/polyfill 的方式会对全局范围造成污染
2. 可能会将未使用的内容一并加载，从而使得编译后的文件存在冗余。

可以通过babel.config.json中对useBuildIns进行配置可以实现按需加载
```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        },
				//核心配置
        "useBuiltIns": "usage"
      }
    ]
  ]
}
```
useBuiltIns属性取值: 
1. 'usage'。根据 targets 中的浏览器版本，只引入浏览器不支持并且有在代码中使用到的 API。
2. 'entry'。根据 targets 中的浏览器版本，只引入浏览器不支持的 API。
3. false 或 不设置（默认）。不通过 preset-env 启用 polyfill。

另一种避免全局范围污染的方法是使用``@babel/plugin-transform-runtime``插件，它依赖core-js-pure实现此功能。

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        },
      }
    ]
  ],
 "plugins": [
    [
      "@babel/plugin-transform-runtime", 
       {
         "corejs": 3
       }
    ] 
  ]
}
```

