---
title: "浅谈MVC"
date: 2020-07-02T19:56:18+08:00
draft: false
tags: ["设计模式", "面向对象"]
categories: ["MVC"]
---

# 浅谈 MVC

第一次了解 MVC 是老韩上 EJB 那门课的时候讨论的，View 由 CSS 和 JSP 控制，Controller 是 Servlet 来做，Model 由会话 Bean 和数据库交互处理，没实际写过还是理解比较浅，这回经过 6 个版本的迭代，增加了一点理解。

<!-- TOC -->

- [浅谈 MVC](#浅谈-mvc)
  - [1. 什么是 MVC](#1-什么是-mvc)
  - [2. 前端 MVC 的历史](#2-前端-mvc-的历史)
  - [3. 最小知识原则+MVC](#3-最小知识原则mvc)
  - [4. 表驱动编程](#4-表驱动编程)
  - [5. 模块化](#5-模块化)

<!-- /TOC -->

## 1. 什么是 MVC

MVC：每个实现特定功能的模块都可以写成三个对象

> 模块化：

1. M-Model 数据模型：负责操作所有数据
2. V-VIew 视图：负责所有 UI 界面
3. C-Controller 控制器：负责其他的所有工作

```JavaScript
class Model {
    //数据初始化
    constructor() {}
    //数据的增删改查
    create() {}
    delete() {}
    update() {}
    get() {}
}
class View{
    //视图初始化
    constructor() {}
    //渲染视图，View = render(data)是React的主要思想之一
    render(data){}
}
class Controller{
    //前端中V和C联系紧密，这回的小项目中，V的初始化就在C中
    constructor() {}
    //各种事务的处理，目前认为最主要的还是事件的绑定和监听
    events:{}
    autoBindEvents() {}
    //通过调用data和render(data)对视图和数据进行更新
    handle(data,render){}
}
```

## 2. 前端 MVC 的历史

1. 2013:Backbone.js 最经典的
2. AngularJS1，基于 MVC 创新的 MVVM，现在也不用了，vue 最开始也是借鉴 MVVM
   1. 双向绑定+MVC
   2. 门槛高，Java 思想
3. Vue0.8 简化版 AngularJS1
   1. 半年左右，Vue1.0 在国内火了
4. 同时期 React，不火没人用，技术好的用 AngularJS1，技术不好的用 Backbone.js
   1. 半年左右，React 突然在国外火了，和 Vue 同时期
   2. Flux 单向绑定
5. Angular2
   1. 默认支持 Dart
   2. 现在加上了 TypeScript
   3. 门槛高
6. Vue2 不再双向绑定，单向绑定，但看起来还像是双向 v-model
7. React 往函数式编程发展
   1. 16.8 函数式 Hook
   2. MV\* + 单行绑定 + 函数式
8. Vue3 借鉴函数式
   1. vue 独有的：单文件模版

## 3. 最小知识原则+MVC

为了实现了 M 和 V 的解耦，我采用了 eventBus 技术

1. eventBus 提供了 on、off 和 trigger 等 API，on 用于监听事件，trigger 用域触发事件
2. eventBus 主要用于对象间通信，使用 eventBus 可以满足最小知识原则，m 和 v 互相不知道对方的细节，但是却可以调用对方的功能

一般通过让 M 继承 eventBus 的方式，使得它们可直接使用，在 M 中提供 update 方法，数据改变时，eventBus 触发事件，在 V 中提供 render(data)方法，在 C 中监听到事件，调用 render(data)即可完成解耦

```JavaScript
//Model继承了eventBus
const m = new Model({
    data: {},
    update(data) {
        m.trigger('m:updated')
    }
})
const v = new View()
//Controller继承了eventBus
const c = new Controller({
    init(){
        c.on('m:updated', () => {
            v.render(m.data)
        })
    }
})
```

## 4. 表驱动编程

我认为，表驱动编程是 DRY 模式:Don't repeat yourself 的一种解决方案
通过表格的固定格式，将变化的内容通过表格记录（一般为哈希表），而不变的内容只需一次编程即可持续使用，避免重复，使得这一部分编程的复杂度固定，而不是随着内容的变化线性增加。

```javascript
events: {
        'click .app1': 'add',
        'click .app2': 'subtract',
        'click .app3': 'multiply',
        'click .app4': 'divide',
        ···
        //可以一直加下去，而autoBindEvents一次编程，终生使用
    }
autoBindEvents() {
        for (let key in events) {
            const event = key.split(' ')[0]
            const element = key.split(' ')[1]
            v.container.on(event, element, c[c.events[key]])
        }
    }
```

## 5. 模块化

自我看来，模块化是复用+解耦，解耦是复用的前提，模块化可以方便的重复利用已有的代码。

---

最近一段时间以来又有些松懈，进入了舒适圈，保持了一些习惯，也丢掉了一些，在压力过大和松懈之间不断平衡，另外最近真的很想待一整天读读书，亲密关系和非暴力沟通，挤一挤时间总会有的，加油！
