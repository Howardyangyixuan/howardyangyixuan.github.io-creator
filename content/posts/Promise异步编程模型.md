---
title: "Promise异步编程模型"
date: 2020-06-30T00:13:28+08:00
draft: false
tags: ["JS","前端"]
categories: ["JS"]
---


# Promise异步编程模型
> Promise优点
> 1. 规定回调的名字或顺序
> 2. 拒绝回调地狱 
> 3. 便于捕获错误

> 初始化一个Promise
> ```javascript
> let promise = new Promise((resolve,reject)=>{
> 	//最初为pending状态
> 	if(success/*成功条件*/) resolve(data)//返回数据
> 	//调用resolve后变为 fulfilled状态
> 	else /*失败*/ reject(error);//返回错误
> 	//调用reject后变为 rejected状态
> })
> ```

<!-- TOC -->

- [Promise异步编程模型](#promise异步编程模型)
	- [1. Promise技术及其背景](#1-promise技术及其背景)
		- [1.1. 什么是Promise技术？](#11-什么是promise技术)
		- [1.2. 什么是异步？](#12-什么是异步)
		- [1.3. Promise出现的背景与意义](#13-promise出现的背景与意义)
	- [2. Promise详解](#2-promise详解)
		- [2.1. Promise的三种状态](#21-promise的三种状态)
		- [2.2. Promise对象的.then方法](#22-promise对象的then方法)
		- [2.3. Promise的注意事项](#23-promise的注意事项)
		- [2.4. Promise对象的其他方法和Promise静态方法](#24-promise对象的其他方法和promise静态方法)
		- [2.5. 用Promise简单封装AJAX](#25-用promise简单封装ajax)
		- [2.6. Axios：最新的AJAX库](#26-axios最新的ajax库)

<!-- /TOC -->
## 1. Promise技术及其背景

### 1.1. 什么是Promise技术？
Promise是目前前端解决异步问题的统一方案。
Promise实际是一种书写回调函数的规范，为异步操作提供统一接口

### 1.2. 什么是异步？
异步：代码的执行不会被阻塞。
- 同步：直接拿到结果，不拿到结果不会离开
- 异步：不能直接拿到结果，异步获取结果的两种方式
  1. 轮询：eg.一直烦你，一直问
  2. 回调：eg.饭店排座，有座位再发送通知

### 1.3. Promise出现的背景与意义
原来的异步编程解决方案缺点显著。
原方案一：回调函数接受两个参数，eg. Node.js
```javascript
fs.readFile('./1.txt',(error,data)=>{
	if(error){console.log('error')return} 
	console.log(data.toString)
})

```
原方案二：搞两个回调函数 
1. 两个回调，每个一个参数
```javascript
ajax('get','/1.json',data=>{},error=>{})
```
2. 两个回调，封装在同一个对象里，键值为success和fail，值为回调函数
```javascript
ajax('get','./1.json',{success:()=>{},fail:()=>{}})
```
以上两种的缺点
1. 不够规范，命名（键值）不固定，success+error / success+fail / done+fail
2. 回调地狱：由于必须在初始化异步操作时定义回调，只有预备好将这个短时间内存在的值作为参数的回调才能接收到它
3. 难以进行错误处理: 不会触发try catch

所以要用Promise
1. 规定回调的名字或顺序
2. 拒绝回调地狱 
3. 便于捕获错误

## 2. Promise详解
初始化一个Promise
```javascript
let promise = new Promise((resolve,reject)=>{
	//最初为pending状态
	if(success/*成功条件*/) resolve(data)//返回数据
	//调用resolve后变为 fulfilled状态
	else /*失败*/ reject(error);//返回错误
	//调用reject后变为 rejected状态
})

```
### 2.1. Promise的三种状态

从待定落定为兑现/拒绝都是不可逆的
- 待定(pending)
- 兑现(fulfilled，有时候也称为“解决”，resolved)
- 拒绝(rejected)

### 2.2. Promise对象的.then方法
Promise.prototype.then()是为Promise实例添加处理程序的主要方法。这个 then()方法接收两个参数:onResolved 处理程序和 onRejected 处理程序。

这两个参数都是可选的，如果提供的话， 则会在期约分别进入“兑现”和“拒绝”状态时执行。

进入“兑现/拒绝”时.then方法的返回值用Promise.resolve()包装，有三种情况
1. 如果resolve函数中显示返回传入值value，返回Promise.resolve(value)
2. 如果resolve函数中没有显示返回值，返回Promise.resolve(undefined)
3. 如果.then方法没有传入onResolved处理程序则原样返回.then前的Promise对象

### 2.3. Promise的注意事项
1. Promise初始化的代码，是同步执行的。
2. return Error('error!!!')不会报错，throw Error('error!!!')报错
3. 在Promise中，返回任意一个非 promise 的值都会被包裹成 promise 对象，
4. .then等方法传入非函数参数时，会忽略参数，直接传递.then前的promise

### 2.4. Promise对象的其他方法和Promise静态方法
1. Promise对象其他方法：catch/finally
	1. .catch(()=>{})方法为.then(null,()=>{})的语法糖
	2. .finally方法无论期约在转换为解决或拒绝状态时，都会执行
2. Promise静态方法：all/race
   1. Promise.all()静态方法创建的期约会在一组期约全部解决之后再解决。这个静态方法接收一个 可迭代对象，返回一个新期约
	 2. Promise.race()静态方法返回一个包装期约，是一组集合中最先解决或拒绝的期约的镜像。这个方法接收一个可迭代对象，返回一个新期约
  
### 2.5. 用Promise简单封装AJAX
```javascript
ajax(method, url, options)=>{
	return new Promise((resolve,reject)=>{
		const {success, fail} = options;
		const request = new XMLHttpRequest();
		request.open(method, url);
		request.onreadystatechange = ()=>{
			if(request.readystate === 4){
				if(request.status < 400){
					resolve(request.response);
				}else{
					reject(request);
				}
			}
		request.send();
		})
	}
}
```

### 2.6. Axios：最新的AJAX库

1. Promise最大的问题：不能被取消，Axios解决了，用编号解决，取消对应的AJAX（请求），Promise还是执行，但是AJAX我不要了
2. 高级用法
	1. JSON自动处理
	2. 请求拦截
	3. 响应拦截
	4. 生成不同实例