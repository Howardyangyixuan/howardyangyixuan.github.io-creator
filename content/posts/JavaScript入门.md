---
title: "JavaScript入门"
date: 2020-05-11T19:54:16+08:00
draft: false
tags: ["JS","前端"]
categories: ["JS"]
---

# JavaScript入门

继续前进！

---
<!-- TOC -->

- [JavaScript入门](#javascript%e5%85%a5%e9%97%a8)
  - [1. JS的诞生](#1-js%e7%9a%84%e8%af%9e%e7%94%9f)
  - [2. JS 的 Prototype](#2-js-%e7%9a%84-prototype)
  - [3. JS 的设计缺陷](#3-js-%e7%9a%84%e8%ae%be%e8%ae%a1%e7%bc%ba%e9%99%b7)
  - [4. JS 基本语法tips](#4-js-%e5%9f%ba%e6%9c%ac%e8%af%ad%e6%b3%95tips)
    - [4.1. && 和 ||](#41--%e5%92%8c)
    - [4.2. fn && fn()](#42-fn--fn)
    - [4.3. label](#43-label)
    - [4.4. setTimeout](#44-settimeout)

<!-- /TOC -->


## 1. JS的诞生

> [阮一峰](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)：1994年，网景公司（Netscape）发布了Navigator浏览器0.9版。这是历史上第一个比较成熟的网络浏览器，轰动一时。但是，这个版本的浏览器只能用来浏览，不具备与访问者互动的能力。因此，网景公司急需一种网页脚本语言，使得浏览器可以与网页互动。工程师Brendan Eich负责开发这种新语言。

恰逢Sun公司主推Java（Write Once, Run Anywhere），两家组成联盟，网景让Java以applet形式入驻浏览器，自己则设计一种语言，蹭Java热点，作者本人其实看不起Java，并且主要研究函数式编程，仅仅10天时间，JS诞生了，Javascript语言实际上是两种语言风格的混合产物，函数式编程+面向对象编程，因为太过仓促，作者甚至称其为 **“C语言和Self语言一夜情的产物”**

## 2. JS 的 Prototype

JavaScript受当时面向对象浪潮的因共享（Java和C++），把数据分成基本类型（primitive）和对象类型（object）两种，而对象间需要产生联系，即数据共享，来节省资源，减少冗余，并且方便修改。使用类的设计的话，JS就是完整的面向对象编程语言了，增加了入门难度。

因此综合考虑，最后有了这两个设计
1. 面向对象的简化：没有类，而只有构造函数
2. 对象间产生联系：为构造函数设置prototype属性
## 3. JS 的设计缺陷
以我目前的接触
1. == 与 ===
2. null 和 undefined
3. 基本类型的包装对象:字符串、数字和布尔值 vs 字符串对象、数字对象和布尔值对象
## 4. JS 基本语法tips
标示符、关键字、注释，都基本和C++一致，只列举一些面试会出的坑。
### 4.1. && 和 ||
|| 不是返回bool值，而是返回第一个不为0的值,或者最后一个元素的值，&&同理
```js
let a = false
> undefined
a || 5
> 5
5 || a
> 5
0 || a
> false
```

### 4.2. fn && fn()
先判断是否有fn，再对fn的返回值进行判断

### 4.3. label
这是一个label，但chrome会优化成对象
```js
{
    foo: 1
}
```

### 4.4. setTimeout
```js
let a = 1
> 1 
if(a===1){
    setTimeout(()=>{console.log(a)})
    a=2
}
> 2
```

最近进展还可以，这周主要时间要去研究4篇GAN，并且复现，然后做实验，改论文。然后继续锻炼，休息看看设计模式，目前就这么安排～加油～
