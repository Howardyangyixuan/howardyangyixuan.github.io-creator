---
title: "JS对象基本用法"
date: 2020-05-20T00:15:26+08:00
draft: false
tags: ["JS","前端"]
categories: ["JS"]
---

# 1. JS对象基本用法

## 1.1. JS对象
1. 定义
    1. 无序数据集合
    2. 键值对
2. 写法
    1. `let obj= {'name':'frank'}`
    2. `let obj= new Object({'name':''frank})`
    3. `console.log({'name':'frank'})`
        1. 如果没有console.log，只是代码块
3. 细节
    1. **!!!键名是字符串**，就算省略引号，还是字符串
    2. 省略的话，就只能写标识符/数字了，加引号可以用中文和emoji
    3. ```Object.keys(obj)```打印键名
    4. ```[]```，可以使用变量，因为```obj[a]=1234```，等于```{[a]:1234}```，注意此时a是变量，```a='name' object['name']```就是1234
    5. Symbol也可以做属性名，不常用，学习迭代使用
4. 对象的隐藏属性__proto__
    1. 储存共有属性组成的对象的地址
    2. 共有属性组成的对象：原型

## 1.2. 原型：
1. 每个对象都有原型
2. 原型存共有属性
3. 对象的原型是一个对象
    1. 根对象的__proto__为nul

## 1.3. 对象的增删改查
### 1.3.1. 删除
1. 方法
    1. ```delete obj.xxx```
    2. ```delete obj\['xxx']```
    3. ```obj.xxx = undefined```，仅置空，不删除
    4. delete删属性
1. 判断是否删除/含有键名，或键值为空
    1. ```'xxx' in obj === false```
    2. ```'xxx' in obj && obj.xxx === undefined```
### 1.3.2. 查看
1. 查看自身属性
    1. ```Objects.keys(obj)```
    2. ```Objects.values(obj)```
    3. ```Objects.entries(obj)```
2. 查看自身+共有属性：只有查会沿着原型链查看
    1. 打印所有包括原型中的内容（共有属性）：```console.dir(obj2)```
    2. obj2.__proto__ 不推荐
    3. ```'name' in obj```
3. 判断是否为自身属性
    1. ```obj.hasOwnProperty('name')```
    2. ```'name' in obj```不能判断是否为自身的属性，会沿原型链查看
4. 查看某一属性
    1. ```obj['key']```
    2. ```obj.key```
    3. !!!注意：**```obj[key]```，变量key值一般不为'key'**
    4. 前两种等价
       1. ```obj.key```不等于```obj[key]```
       2. ```let name='frank', obj\[name]=obj\['frank']```
### 1.3.3. 修改/增加
1. 直接赋值
    1. 查看的方式，直接赋值
2. 批量赋值
    1. ```Object.assign(obj,{p1:1,p2:2,p3:3})```,es6新api
3. 增加或修改共有属性
    1. 读的时候可以读到
    2. 写的时候只能写到自己身上
    3. 偏要改
        1. 通过obj.__proto__.toString修改
        2. 不要使用__proto__
        3. 如果要改，用Object.prototype
        4. 一般不要改
4. 原型链
    1. ```var common={'国籍'：'中国'}```
    2. ```Object.create(common,{name:{value:'frank'} } )```
    3. 使用__proto__性能低（存疑，待细查）

