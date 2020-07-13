---
title: "入门Vue"
date: 2020-07-13T19:31:24+08:00
draft: false
tags: ["Vue", "前端"]
categories: ["前端框架"]
---

# 入门 Vue

<!-- TOC -->

- [入门 Vue](#入门-vue)
  - [1. Vue 重要历史节点](#1-vue-重要历史节点)
  - [2. Vue 作者简介](#2-vue-作者简介)
  - [3. Vue 的现状](#3-vue-的现状)
  - [4. 创建 Vue 项目](#4-创建-vue-项目)
  - [5. 如何使用 Vue 实例](#5-如何使用-vue-实例)
  - [6. Vue 完整版 vs 运行时版本](#6-vue-完整版-vs-运行时版本)
  - [7. codesandbox：最快的 vue 开发环境搭建方法](#7-codesandbox最快的-vue-开发环境搭建方法)

<!-- /TOC -->

## 1. Vue 重要历史节点

2015 1.0 MVVM
2016 2.0 看上去是 MVVM 实际不是
2019 2.6 Vue2 的最后一个大版本号
2020 3.0 完全不是 MVVM

## 2. Vue 作者简介

作者：尤雨溪

作品：

1. vue
2. vuex
3. vue router
4. @vue/cli

团队/助理：蒋豪群

启示：

1. 把目标放在最厉害的工程师，会有更多可能性
2. 程序员工资高：产生价值，一个 Vue 提高了大家工作效率工资上不封顶，取决于价值，高级程序员封装！
3. 英文要好！

## 3. Vue 的现状

用 Vue 大部分是中国人：80%，主要原因，作者中国人，中文文档很好

## 4. 创建 Vue 项目

1. 方法一：使用 vue/cli
2. 方法二：自己用 webpack 配置

## 5. 如何使用 Vue 实例

1. 方法一：从 HTML 中得到视图
   1. CDN 引用 vue.js 或 vue.min.js
   2. import 引用 vue.js
2. 方法二：用 JS 构建视图
   1. vue.runtime.js，更加独立
   2. 通过 h 即 createElement 来创建 DOM
3. 方法三：使用 vue-loader
   1. 开发者正常使用完整版写法，使用 webpack 的 vue-loader 进行转译，变为运行时版本
   2. 用户使用 vue.runtime.js，最小体积，并且可以运行

## 6. Vue 完整版 vs 运行时版本

![Vue 版本说明](/images/vue版本说明.png)

完整版：有 compiler 将字符串转换为 DOM 节点，可以从 html 中获取视图，类似在页面中进行编辑

运行时版本：不支持从 html 中获取视图，需要使用.vue 配合构造函数。

目的：让用户端空间占用空间最小

使用方法：

1. template: HTML 内容模板（`<template>`）元素是一种用于保存客户端内容机制，该内容在加载页面时不会呈现，但随后可在运行时使用 JavaScript 实例化。Vue 中将一个字符串模板作为 Vue 实例的标识使用，模板将会替换挂载的元素，挂载元素的内容都将被忽略，除非模板的内容有分发插槽（分发插槽 v-slot 有待进一步学习）

> 在 Demo.vue 文件中提供模版和方法，在 main.js 用 vue 实例化

Demo.vue

```js
<template>
  <div class="red">
    {{n}}
    <button @click="add">+1</button>
  </div>
</template>
<script>
export default {
  data() {
    return {
      n: 0
    };
  },
  methods: {
    add() {
      this.n += 1;
    }
  }
};
</script>
<style scoped>
.red {
  color: red;
}
</style>

```

main.js

```js
import Demo from "./Demo.vue";
new Vue({
  el: "#app",
  data: {
    n: 0,
  },
  render(h) {
    console.log(h(Demo));
    return h(Demo);
  },
});
```

2. render 函数: 字符串模板的代替方案，允许你发挥 JavaScript 最大的编程能力。该渲染函数接收一个 createElement 方法作为第一个参数用来创建 VNode。
   如果组件是一个函数组件，渲染函数还会接收一个额外的 context 参数，为没有实例的函数组件提供上下文信息
   main.js

```js
new Vue({
  el: "#app",
  render(h) {
    return h("div", [this.n, h("button", { on: { click: this.add } }, "+1")]);
  },
```

## 7. codesandbox：最快的 vue 开发环境搭建方法

点击链接，直接使用[codesandbox](https://codesandbox.io/)，根据提示选择 vue，直接构建项目即可，如需本地使用，下载 zip 即可

---

2020.7.10

毕业了

不管是否留恋，都要开始新生活了
