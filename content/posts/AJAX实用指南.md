---
title: "AJAX实用指南"
date: 2020-06-15T00:15:26+08:00
draft: false
tags: ["JS","前端"]
categories: ["JS"]
---

# AJAX实用指南 
<!-- TOC -->

- [AJAX实用指南](#ajax实用指南)
	- [1. AJAX技术及其背景](#1-ajax技术及其背景)
		- [1.1. 什么是AJAX技术？](#11-什么是ajax技术)
		- [1.2. 背景](#12-背景)
	- [2. 如何使用AJAX](#2-如何使用ajax)
		- [2.1. AJAX四步走](#21-ajax四步走)
		- [2.2. 注意](#22-注意)

<!-- /TOC -->
## 1. AJAX技术及其背景

### 1.1. 什么是AJAX技术？
一言以蔽之，AJAX技术就是用JS发请求和收响应

### 1.2. 背景
AJAX最初是浏览器上的功能，浏览器可以输入url可以发请求、收响应展示页面，既然浏览器可以，能否也提供给开发者使用？

于是浏览器在window对象上加了XMLHttpRequest函数，用于构造XMLHttpRequest对象，JS可以通过这个对象发送请求和接受响应


## 2. 如何使用AJAX

### 2.1. AJAX四步走 

1. 构造请求对象：创建HttpRequest对象
```javascript
const request = new XMLHttpRequest()
```
2. 确定请求方法和地址：调用对象的open方法
```javascript
request.open(method, url)//专业的前端只用这两个参数
//使用 POST 发送数据，需要设置请求的MIME类型。
request.setRequestHeader('Content-Type', 'application/json');
```
3. 添加事件监听：监听request对象的onreadystatechange事件
   1. request.onreadystatechange=()=>{}
   2. 里面写if(request.readystate ===4){}//4为成功下载后，此时得到的request.respond才是完整的
   3. 成功下载后，还要看是否成功请求if(request.status>=200 && request.status<300)//2开头的都是成功的
	 4. 其中readystate
        1. 0:UNSENT 代理被创建，但尚未调用open()方法
        2. 1:OPENED open()方法已经被调用
        3. 2:HEADERS_RECEIVED send()方法已经被调用，并且头部和状态已经可获得
        4. 3:LOADING 下载中；responseText属性已经包含部分数据
        5. 4:DONE 下载操作已完成
```javascript
request.onreadystatechange = () => {
	if (request.readyState === 4) {
		if (request.status >= 200 && request.status < 300) {
			//处理响应，request.response
			const object = JSON.parse(request.response);
			document.querySelector(".myName").textContent = object.name;
		}
	}
```
4. 发送请求：调用对象的send方法
```javascript
request.send();
//如果使用 POST 则在send中附上内容
request.send(data);
```

### 2.2. 注意

1. 设置正确的Content-Type
2. 根据不同类型进行解析
    1. css得到后放到style标签
    2. js得到后放到script标签
    3. HTML，innerHTML和DOM API插入
    4. XML使用responseXML和DOM API
    5. 不同类型，使用不同类型解析方法