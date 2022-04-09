---
title: "XSS实战"
date: 2022-04-06T08:50:34+08:00
draft: false
---

# XSS实战

> 看了[美团安全系列一：如何防止XSS](https://segmentfault.com/a/1190000016551188?utm_source=sf-similar-article)之后，根据指引去[XSS Challenge](https://xss-quiz.int21h.jp/ranking.php) 和 [XSS Game](https://xss.pwnfunction.com/)实战了一下，然后来总结一下到底什么是XSS攻击以及常见的攻击和预防的方法。

## 什么是 XSS
XSS跨站脚本攻击(Cross-Site Scripting，为与 CSS 区分而简称XSS）是一种代码注入攻击。

通过在目标网站上注入恶意脚本并在用户浏览器端运行，攻击者可以盗窃用户的Cookie、SessionID 等敏感信息 ，甚至以此为入口，进而危害整个的服务器数据安全。

## 造成XSS漏洞的原因
XSS 的本质是：来自不可信来源的信息包含恶意代码，未经过滤，与网站正常的代码混在一起并被执行。

其中常见的不可信来源有两大类
1. UGC信息：如用户评论，聊天等
2. 请求信息：POST参数，URL参数，Referer，Cookie等

## XSS攻击的分类
1. 存储型：UGC信息（如用户评论）中含有恶意代码，并被存放在服务器中，用户访问该页面时，恶意代码执行。攻击者实际是以用户身份进行攻击，并且存储型攻击具有持久性，恶意代码在被处理前持续有效。
2. 反射型：请求信息中含有恶意代码，被后端处理后，被整合到正常页面中返回给用户，造成恶意代码执行。由于恶意代码存在与请求中，攻击者需要诱导用户主动打开恶意的 URL 才能生效，是一次性的攻击。
3. DOM型：与反射型类似，URL中含有恶意代码，但是由前端直接端处理后，被整合到正常页面中解析执行。DOM型攻击触发方式与反射型一致，也是一次性攻击。

## 常见XSS攻击思路
1. 找注入点
2. 注入代码
    1. 闭合上下标签，注入scirpt，"><script></script><
    2. 在url中注入，?redirect_to=javascript:alert(xxx);
    3. src或img中onload/onerror=alert(xxx)
3. 如果存在过滤
    1. 转义字符\
       1. [JSFuck](https://github.com/aemkei/jsfuck)
    2. HTML实体符号
        1. 如果屏蔽<>，则无法加入新的script标签，但可以首先用"闭合上一属性，新增可以使用js的其他属性（事件处理函数），onclick/onmouseover =alert(document.domain) 
    3. 过滤字符，如/，domain，cookie
        1. 利用一次过滤，通过双写：如dodomainmain和//，产生被过滤的字符
        2. 转unicode或16进制编码
           1. unicode \u
           2. 16进制 \x

## 对应的防御措施

1. 开发时注意事项
   1. 对cookie保护：对重要的cookie设置HttpOnly，限制 Cookie 通过 JavaScript 经由 Document.cookie 属性、XMLHttpRequest 和 Request APIs 进行访问。
   2. 对用户输入内容的防范：通过纯前端渲染，将代码和数据分离，并对用户输入内容进行转义和过滤，具体如下：
      1. 编码： 对用户输入的数据进行HTML Entity编码，使用Vue/React可以在前端 render 阶段避免 innerHTML、outerHTML 的 XSS 隐患
      2. 过滤： 移除用户上传的DOM属性，如onerror，onclick等，移除用户上传的Style节点、Script节点、 iframe节点等
			3. 限制：如控制用户输入内容长度控制等
2. XSS系统性防御措施：
   1. 设置CSP网页安全政策（Content Security Policy：CSP 的实质就是白名单制度，开发者提供配置明确告诉客户端，哪些外部资源可以加载和执行。可以通过HTTP 头信息的Content-Security-Policy的字段或通过网页的<meta>标签进行配置。
	 2. XSS检测工具：使用 [Arachni](https://github.com/Arachni/arachni)、[Mozilla HTTP Observatory](https://github.com/mozilla/http-observatory/)等XSS自动检测工具。

<hr>
从复习Cookie相关知识延伸过来，感觉收获很大，理解也更深入了，很棒继续加油！