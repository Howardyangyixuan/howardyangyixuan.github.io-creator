---
title: "TS泛型的使用"
date: 2021-04-05T23:14:00+08:00
tags: ["JS","前端"]
categories: ["JS"]
draft: false
---

# TS范型的使用

> 软件工程中，我们不仅要创建一致的定义良好的 API，同时也要考虑可重用性。 组件不仅能够支持当前的数据类型，同时也能支持未来的数据类型，这在创建大型系统时为你提供了十分灵活的功能。

- [TS范型的使用](#ts范型的使用)
	- [1. 泛型是什么？有什么意义？](#1-泛型是什么有什么意义)
		- [1.1 定义](#11-定义)
		- [1.2 意义](#12-意义)
		- [1.3 TS中的泛型](#13-ts中的泛型)
	- [2. TS泛型基本元素](#2-ts泛型基本元素)
		- [2.1 泛型变量](#21-泛型变量)
		- [2.2 泛型函数](#22-泛型函数)
		- [2.3 泛型接口](#23-泛型接口)
		- [2.3 泛型类](#23-泛型类)
		- [2.4 泛型约束](#24-泛型约束)
	- [3. TS泛型的操作](#3-ts泛型的操作)
		- [3.1 类型别名](#31-类型别名)
		- [3.2 联合](#32-联合)
		- [3.3 交叉](#33-交叉)

## 1. 泛型是什么？有什么意义？

### 1.1 定义
> 泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

### 1.2 意义 

> 泛型使得组件的可重用性得到极大增强，使用泛型一个组件可以支持多种数据结构，开发者也可以自定义数据结构，大大提升了灵活性。

### 1.3 TS中的泛型

对于未知具体类型，但存在内在关联的数据结构，使用泛型可以充分表达约束，而又不陷入TS不必要的刻板，同时避免TypeScript成为AnyScript，起到提示和预防bug的效果，

下面结合实例，简单介绍TS泛型的使用。

## 2. TS泛型基本元素
### 2.1 泛型变量
泛型变量顾名思义也是一个变量，习惯上用T表示，语法上通\<T\>传递给函数或类，在参数列表中和返回值中直接使用
```TypeScript
function echo<T>(anything: T): T {
    return anything;
}
```
可以将其理解为一个类型占位符，在使用它的时候，为避免出错应当把这些参数当做是任意或所有类型，而不能默认其具有某些类型特有的属性，如数组的长度等

如之前所说，实际上 T 可以用任何符合语法的名称代替。除了 T 之外，以下是常见泛型变量代表的意思：

- K（Key）：表示对象中的键类型；
- V（Value）：表示对象中的值类型；
- E（Element）：表示元素类型。

### 2.2 泛型函数
泛型函数使用像其他强类型语言的函数声明一样，以下是几种等价的写法：
```ts
function echo<T>(anything: T): T {
    return anything;
}

let another_echo: <T>(anything: T) => T = echo;

let yet_another_echo: {<T>(anything: T): T} = echo;
```

### 2.3 泛型接口
如果多个函数具有同样的泛型变量，或者可以泛化为统一的泛型函数，可以使用泛型接口抽象出一个统一的、可复用的泛型函数

```ts
interface echoFn {
    <T>(anything: T): T;
}

let echo_from_interface: echoFn = echo;

let echo_number: echoFn<number> = echo;
let echo_string: echoFn<number> = echo;
```

### 2.3 泛型类
泛型类在语法上没有太多新奇，同样使用<>括起泛型类型，跟在类名后面。

具体的使用场景，TS官网的例子很生动。

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```
GenericNumber类，即广义数字的概念比较直观，它的类型不局限于number类型，也可以使用字符串或其它类型。

```ts

let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function(x, y) { return x + y; };

console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));
```
与接口一样，直接把泛型类型放在类后面，可以帮助我们确认类的所有属性都在使用相同的类型。

需要注意的是，类有两部分：静态部分和实例部分。 泛型类指的是实例部分的类型，**类的静态属性不能使用这个泛型类型**。
### 2.4 泛型约束
文章开篇提到，在使用泛型变量时，应该将其视为任意或所有类型，但实际应用中我们需要抽离一些具有统一特征的数据结构，泛型本身不足以满足要求时，可以使用泛型约束进行细化。

如希望处理任意带有.length属性的类型，可以通过如下约束实现。
```ts
interface Length {
    length: number;
}

function echo<T extends Length>(anythingWithLength: T): T {
    console.log(anythingWithLength.length);
    return anythingWithLength;
}
```

## 3. TS泛型的操作
列举一些常用的TS泛型操作，用于简化泛型定义，其中type和interface的区别，另起一篇文章解释，暂且可以认为仅为语法不同。
### 3.1 类型别名
使用别名一方面可以简化重复书写，另一方面也使递归定义成为可能
```ts
//树的定义，第一步不完全正确
type Tree<T> = {
    value: T;          // value是T类型
    left: Tree<T>;     // 通过类型别名实现递归定义
    right: Tree<T>;
}

```
### 3.2 联合
泛型在与其他类型共同使用表示“或”关系时，可以使用联合操作符号。
```ts
//树的定义，修正
type Tree<T> = {
    value: T;          // value是T类型
    left: Tree<T> | null;     // 通过类型别名实现递归定义
    right: Tree<T> | null;
}
```
### 3.3 交叉
泛型在与其他类型共同使用表示“与”关系时，可以使用交叉操作符号。
```ts
//链表的定义，示意
type LinkedList<T> = T & { next: LinkedList<T> };
```