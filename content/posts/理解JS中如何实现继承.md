---
title: "理解JS中如何实现继承"
date: 2022-01-01T13:50:09+08:00
draft: false
tags: ["JS","前端"]
categories: ["JS"]
---

# 理解JS中如何实现继承

> 很多面向对象语言都支持两种继承:接口继承和实现继承。 前者只继承方法签名，后者继承实际的方法。接口继承在 ECMAScript 中是不可能的，因为函数没有签名。实现继承是 ECMAScript 唯一支持的继承方式，而这主要是通过原型链实现的。

> TLDR: JS中继承终极方法即寄生组合式，组合式继承模式的核心就是产生一个__proto__指向父类原型的对象，两种方法1.组合式：使用父类型构造函数，但性能差，2.寄生组合式：生成一个prototype指向父类原型的轻量构造函数，使用该构造函数生成对象

- [理解JS中如何实现继承](#理解js中如何实现继承)
	- [1.基本概念](#1基本概念)
		- [1.1 为什么需要继承](#11-为什么需要继承)
		- [1.2 JS中的原型](#12-js中的原型)
		- [1.3 JS中创建对象的模式](#13-js中创建对象的模式)
			- [1.3.1 工厂模式](#131-工厂模式)
			- [1.3.2 构造函数实现工厂模式](#132-构造函数实现工厂模式)
			- [1.3.3 解决构造函数模式,无法复用函数;](#133-解决构造函数模式无法复用函数)
			- [1.3.4 原型模式](#134-原型模式)
	- [2. JS中实现继承的6种模式](#2-js中实现继承的6种模式)
		- [2.1 原型链](#21-原型链)
		- [2.2 盗用构造函数——经典继承](#22-盗用构造函数经典继承)
		- [2.3 组合式继承——原型链+盗用构造函数](#23-组合式继承原型链盗用构造函数)
		- [2.4 原型式继承](#24-原型式继承)
		- [2.5 寄生式继承](#25-寄生式继承)
		- [2.6 寄生组合式继承](#26-寄生组合式继承)

## 1.基本概念 

### 1.1 为什么需要继承 

> 使用继承可以复用并扩展原有代码，减少重复性工作。

### 1.2 JS中的原型

> 核心三点：
> 1. 每个构造函数有prototype指针指向原型对象：F.prototype = 原型对象
> 2. 原型对象有指针constructor指回构造函数： 原型对象.constructor = F
> 3. 由构造函数生成的实例,有指针__proto__指向原型对象: f.__ proto__ = 原型对象

### 1.3 JS中创建对象的模式

构造对象是JS实现继承的基石，使用Object 构造函数（如Object.defineProperty）或对象字面量可以方便地创建对象，但这些方式也有明显不足:创建具有同样接口的多个对象需要重复编写很多代码。下面介绍几种在继承中常用的构造对象模式。

#### 1.3.1 工厂模式
```js
    console.log('1.工厂模式');
    let createPerson = function(name,age){
        let obj = new Object();
        obj.name = name;
        obj.age = age;
        obj.sayName = function(){
            console.log('- I am',this.name);
        };
        return obj;
    }
    let factoryPerson = createPerson('factory',1);
    factoryPerson.sayName();
```

#### 1.3.2 构造函数实现工厂模式
```js
    console.log('2.构造函数实现工厂模式');
    // function PersonFactory(name,age){
    let PersonFactory = function(name,age){
        this.name = name;
        this.age = age;
        this.sayName = function(){
            console.log('- I am',this.name);
        }
        //默认return this
    }
    let factoryPerson = new PersonFactory('factoryByNew',2)
    factoryPerson.sayName();
    let bool = factoryPerson.__proto__ === PersonFactory.prototype;
    console.log('- factoryPerson.__proto__ === PersonFactory.prototype',bool);
    console.log('- 问题: 函数会重复声明,无法复用');
    let factoryPerson1 = new PersonFactory('factoryByNew1',2);
    bool = factoryPerson.sayName === factoryPerson1.sayName;
    console.log('- factoryPerson.sayName === factoryPerson1.sayName',bool);
```
#### 1.3.3 解决构造函数模式,无法复用函数;
```js
    console.log('3.解决构造函数模式,无法复用函数');
    let sayName = function(){
        console.log('- I am',this.name);
    }
    let ShareFactory = function(name,age){
        this.name = name;
        this.age = age;
        this.sayName = sayName;
    }
    let shareFactory1 = new ShareFactory('shareFactory1',3)
    let shareFactory2 = new ShareFactory('shareFactory2',3)
    let bool = shareFactory1.sayName === shareFactory2.sayName;
    console.log('- shareFactory1.sayName === shareFactory2.sayName',bool);
```
#### 1.3.4 原型模式
```js
    function proto(){}
    proto.prototype.name = 'proto';
    proto.prototype.age = 4;
    proto.prototype.sayName = sayName;
    let protoPerson1 = new proto(); 
    let protoPerson2 = new proto(); 
    let bool = protoPerson1.sayName === protoPerson2.sayName;
    console.log('- protoPerson1.sayName === protoPerson2.sayName',bool);
    console.log('- 问题: 属性也会复用,没有独立的实例属性');
    let bool = protoPerson1.friend === protoPerson2.friend;
    console.log('- protoPerson1.friend === protoPerson2.friend',bool);

```
下面结合示例代码，JS中实现继承的6种模式。

## 2. JS中实现继承的6种模式

> JS中实现继承的具体目标就是：实现子类型实例能够访问父类原型上的方法和属性，同时有希望具有自己独享的实例属性。
### 2.1 原型链 
原型链是实现继承的一种方式，具体思路如下：A和B是两个构造函数,通过让一个对象同时拥有两个身份,既是B的原型对象,又是A的实例对象,连接两个构造函数,实现B的实例继承A原型上的属性和方法。
```js
   let Father = function(){
        this.name = "father";
        this.friends = [1,2]; 
        this.other = arguments;

    };
    let sayName = function(){
        console.log('- I am',this.name);
    }
    let sayHi = function(){
        console.log('- Hi, my name is:',this.name);
    }
    Father.prototype.sayName = sayName;
    let Son = function(){
        this.age = 18;
    };
    Son.prototype = new Father();
    let son1 = new Son();
    son1.sayName();
    let son2 = new Son()
```
但该方式存在两个问题
1. 问题1: 实例属性变成了原型属性,产生错误的共享数据 
2. 问题2: 无法向父类型传参生成独享的属性,传参都会使子类型所有实例发生改变

### 2.2 盗用构造函数——经典继承
原型链是实现继承的一种方式，具体思路如下：A和B是两个构造函数,通过让一个对象同时拥有两个身份,既是B的原型对象,又是A的实例对象,连接两个构造函数,实现B的实例继承A原型上的属性和方法。
```js
   console.log('2.盗用构造函数——经典继承');
    console.log('- 在子类型构造函数中，调用父类型构造函数，实现继承并独享父类型的实例对象属性');
    Grandpa =  function(){
        this.money = 10;
    }
    Father = function(name){
        Grandpa.call(this);
        this.friends = [1,2];
        this.name = name;
    }
    Father.prototype.sayName = sayName;
    Son = function(name,age){
        Father.call(this,name);
        this.age = age;
        this.sayHi = sayHi;
    }
    son1 = new Son('son1');
    son2 = new Son('son2');
    father1 = new Father('father');
    son1.friends.push(3);
    console.log('- son1.friends === son2.friends',son1.friends === son2.friends);
    console.log('- 并且可以传递参数');
    son1.sayHi();
    son2.sayHi();
```
但该方式存在两个问题
1. 问题1: 无法在保持代码紧凑的同时重用函数（和构造函数模式问题相同） 
2. 问题2: 无法访问父类原型上的方法,只能逐层使用构造函数模式
```js
    console.log('问题1: 无法在保持代码紧凑的同时重用函数（和构造函数模式问题相同）');
    console.log('问题2: 无法访问父类原型上的方法,只能逐层使用构造函数模式');
    console.log('- sayName in father1','sayName' in father1);
    console.log('- sayName in son1','sayName' in son1);
    console.log('- money in son1 ','money' in son1);
```
### 2.3 组合式继承——原型链+盗用构造函数
融合前两种方式的优点，组合式继承通过原型链继承原型上的方法和属性，通过构造函数继承实例属性
```js
    console.log('3.组合继承——原型链+盗用构造函数');
    console.log('通过原型链继承原型上的方法和属性，通过构造函数继承实例属性（可独享）');
    Father = function(name){
        this.friends = [1,2];
        this.name = name;
    }
    Father.prototype.sayName = sayName;
    Father.prototype.lastName = 'Yang';
    Son = function(name,age){
        Father.call(this,name);
        this.age = age;
    }
    Son.prototype = new Father();
    Son.prototype.sayHi = sayHi;
    son1 = new Son('mixin-son');
    father1 = new Father('father');
    console.log('- sayName in father1','sayName' in father1);
    console.log('- sayName in son1','sayName' in son1);
    son1.sayHi();
```
然而组合式继承仍有两个问题
1. 问题1: 两次调用父类构造函数会产生的效率问题
2. 问题2: 第一滴调用时，子类原型对象上增加了不必要的，由父类型构造函数产生的属性
```js
  console.log('- 第1次Son.prototype = new Father()');
	console.log('- 第2次Father.call(this)')
```
组合式继承在功能上已经满足了大部分要求，为了解决其性能问题和不必要的副作用，通过“原型式继承”->“寄生式继承”->最终得到“寄生组合式继承”解决了该问题。

### 2.4 原型式继承
```js
    console.log('4.原型式继承');
    console.log('不自定义类型，即不通过构造函数生成原型对象，而是直接将需要共享信息的对象作为原型对象，实现继承');
    console.log('实际上是一种创建对象的方式，与原型模式相同');
    function object(o){
        function F(){};
        F.prototype = o;
        return new F();
    }
    let father = {
        fisrtname : 'Yang',
        name : 'father',
        friends : [1,2]
    };
    son1 = object(father);
    son2 = object(father);
    son3 = Object.create(father);
    console.log('- objectson1.friends === son2.friends',son1.friends === son2.friends);
    console.log('- Object.create() son3.friends === son1.friends',son3.friends === son1.friends);
```
### 2.5 寄生式继承
```js
    console.log('5.寄生式继承');
    console.log('同样直接将需要被继承/共享信息的对象作为原型对象，融合工厂模式，直接返回一个增加了方法的对象');
    function createSon(father){
        let clone = object(father);//原型式继承
        clone.sayHi = function(){
            console.log('- I am ',this.name);
        }
        return clone;
    }
    son1 = createSon(father);
    son2 = createSon(father);
    son1.sayHi();
    console.log('问题: 无法在保持代码紧凑的同时重用函数（和构造函数模式问题相同）');
```
### 2.6 寄生组合式继承
寄生组合式继承 vs 组合式继承：
组合式继承模式的核心就是产生一个__proto__指向父类原型的对象
两种方法
1. 组合式：使用父类型构造函数，但性能差
2. 寄生组合式：生成一个prototype指向父类原型的轻量构造函数，使用该构造函数生成对象
```js
   console.log('6.寄生组合式继承——组合式继承优化');
    console.log('避免组合式继承，两次调用父类构造函数产生的效率问题');
    console.log('避免子类原型对象上不必要的，由父类型构造函数产生的属性');
    console.log('- 第1次Son.prototype = new Father()');
    console.log('- 第2次Father.call(this)');
    console.log('避免第一次，通过调用父类构造函数给子类原型赋值，而是使用寄生式继承，使用父类原型对象产生赋值给子类原，实现继承父类原型');
    function inheritPrototype(Son, Father){
        let prototype = object(Father.prototype);
        prototype.constructor = Son;
        Son.prototype = prototype;
    }
    Father = function(name){
        this.name = name;
    }
    Father.prototype.sayName = sayName;
    Son = function(name,age){
        Father.call(this,name);
        this.age = age;
    }
    inheritPrototype(Son,Father);
    Son.prototype.sayHi = sayHi;
    son1 = new Son('son1',1);
    son2 = new Son('son2',2);
    son1.sayName();
    son2.sayName();
    son2.sayHi();
```