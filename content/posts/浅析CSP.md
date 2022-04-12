---
title: "浅析CSP"
date: 2022-04-10T09:59:38+08:00
draft: false
---

# 浅析CSP
> 上文[XSS实战](http://yangyixuan.icu/posts/xss%E5%AE%9E%E6%88%98/)我们提到CSP是内容安全策略是一种系统性预防XSS攻击的手段，下面就来具体了解一下CSP以及具体如何设置CSP

<!-- TOC -->

- [浅析CSP](#浅析csp)
	- [什么是CSP](#什么是csp)
	- [CSP的设置方法和常用属性](#csp的设置方法和常用属性)
		- [CSP的设置方法](#csp的设置方法)
		- [Cookie常用属性](#cookie常用属性)
		- [注意事项](#注意事项)
	- [CSP常用设置方案](#csp常用设置方案)
		- [示例 1](#示例-1)
		- [示例 2](#示例-2)
		- [示例 3](#示例-3)
		- [示例 4](#示例-4)

<!-- /TOC -->
## 什么是CSP
CSP内容安全策略(Content Security Policy)是通过白名单过滤的方式，用于预防各种跨站攻击和数据注入攻击的一种安全策略。

具体而言，CSP配置限制浏览器获取页面资源的来源，如一个可以上传文件和显示图片页面，应该允许图片来自任何地方，但限制表单的action属性只可以赋值为指定的站点。
## CSP的设置方法和常用属性
### CSP的设置方法
通过服务器响应Http头部，设置CSP
```javascript
response.setHeader("Content-Security-Policy","具体policy");
```
另一种方式是在页面中使用 ``<meta>``  来配置该策略
```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">
```
### Cookie常用属性
```html
Content-Security-Policy:
	<!-- 设置时多个source可并列 -->
	<!-- 如果其他白名单（xxx-src）不存在，浏览器会应用该默认值 -->
	default-src <source>;
	<!-- 内联脚本白名单 -->
	script-src <source>;
	<!-- 内联样式白名单 -->
	style-src <source>;
	<!-- 图片白名单 -->
	img-src <source>;
	<!-- 媒体白名单 -->
	media-src <source>;
	<!-- 子元素白名单 -->
	child-src <source>;
	<!-- 链接白名单 -->
	connect-src <source>;
	<!-- 字体白名单 -->
	font-src <source>;
	<!-- 将违反报告的行为报告到对应URL，不推荐使用，因为部分浏览器已经不再支持 -->
	report-uri <url>;
```
源 ``<source>``可以为以下值
  1. ``<host-source>``： 
		1. 主机：以域名或者 IP 地址表示的主机名，外加可选的协议名和端口号。站点地址中可能会包含一个可选的前置通配符（星号 \'\*\'），同时也可以将通配符（也是\'\*\'）应用于端口号，表示在这个源中可以使用任意合法的端口号。

			```text
			例如：
			http://*.yangyixuan.com: 匹配从使用 http: 的 yangyixuan.com 的任意子域的资源加载。 

			yangyixuan.com:*:匹配对 yangyixuan.com 上的任意端口的访问。

			https://yangyixuan.com: 匹配对使用了 https协议 的 yangyixuan.com 的访问。
			```
		2. 路径：以/结尾的路径，匹配该目录下所有路径，否则只匹配当前路径
			```text
			例如：
			yangyixuan.com/api/ 可以匹配 yangyixuan.com/api/posts/getPost.js
			而 yangyixuan.com/api/posts/getPost.js只会匹配该路径
			```

   2. ``<scheme-source>``：协议名。必须带有冒号，不要有单引号
      1. http:
      2. https:
      3. data: URIs 但不推荐使用，因为攻击者可以注入任意 data: URI。
      4. mediastream: URIs
      5. blob: URIs 
      6. filesystem: URIs 
		```text
		例如： 只允许通过https协议加载图片资源
		Content-Security-Policy: img-src https: 
		```

   3. 关键字
      1. 'self'：指向与要保护的文件所在的源，包括相同的协议与端口号。
      2. 'none'：不允许任何内容。
      3. 'strict-dynamic'：strict-dynamic 信任含有指定标记的内联脚本(一个附加的随机数或散列)，并且由该脚本加载的所有脚本也被标记并允许加载。与此同时，任何白名单以及源表达式中（例如 'self'和'unsafe-inline' 不包含该值的内联脚本都会都会被拒绝执行。
			```text
			例如：同时设置strict-dynamic和某一源 
			Content-Security-Policy: script-src 'strict-dynamic' 'nonce-R4nd0m' http://yangyixuan.com
			```
			```html
			允许执行loader.js和其加载的其他脚本
			<script nonce="R4nd0m" src="http://other.com/loader.js">
			拒绝执行来自指定源没有nonce的脚本
			<script src="http://yangyixuan.com/loader.js">

			```
   4. 不安全的关键字：
      1. 'unsafe-inline'： 允许使用内联资源，例如内联 ``<script>``  元素（javascript: URL）、内联事件处理器以及内联 ``<style>`` 元素。
      2. 'unsafe-eval'：允许使用 eval() 以及相似的函数来从字符串创建代码。
   5. 不得已要使用内联脚本时，较为安全的方式
      1. 'nonce-<base64值>'：特定使用一次性加密内联脚本的白名单。服务器必须在每一次传输政策时生成唯一的一次性值。否则将存在绕过资源政策的可能。
		```text
		例如： 只允许特定内联脚本加载
		Content-Security-Policy: script-src 'nonce-2726c7f26c'
		```
		```js
		那么该内联脚本可以执行
		<script nonce="2726c7f26c">
		var inline = 1;
		</script>
		```
      2. ``<hash-source>``： 使用 sha256、sha384 或 sha512 编码过的内联脚本或样式。其由用短划线分隔的两部分组成: 用于创建哈希的加密算法, 以及脚本或样式base64编码的哈希值。当生成哈希值的时候，不要包含 ``<script>`` 或 ``<style>`` 标签，同时注意字母大小写与空格（包括首尾空格）都是会影响生成的结果的。 
		```text
		例如： 只允许特定内联脚本加载：首先对特定脚本"var inline = 1;"使用加密算法编码，然后写入CSP配置
		Content-Security-Policy: script-src 'sha256-B2yPHKaXnvFWtRChIbabYmUBFZdVfKKXHbWtWidDVF8=c'
		```
		```js
		那么该内联脚本可以执行
		<script>var inline = 1;</script>
		```
 
 ### 注意事项
1. script-src是必设的，除非设置了default-src。因为攻击者只要能注入脚本，其他限制都可以规避。
2. script-src不能使用unsafe-inline关键字，也不能允许设置data:URL，否则会产生类似如下的XSS安全漏洞：
```html
<img src="x" onerror="evil()">
<script src="data:text/javascript,evil()"></script>
```

## CSP常用设置方案
一些常用的安全策略方案示例如下：

### 示例 1
一个网站管理者想要所有内容均来自站点的同一个源 (不包括其子域名)
```http
Content-Security-Policy: default-src 'self'
```
### 示例 2
一个网站管理者允许内容来自信任的域名及其子域名 (域名不必须与CSP设置所在的域名相同)

```http
Content-Security-Policy: default-src 'self' *.trusted.com
```
### 示例 3
一个网站管理者允许网页应用的用户在他们自己的内容中包含来自任何源的图片, 但是限制音频或视频需从信任的资源提供者(获得)，所有脚本必须从特定主机服务器获取可信的代码.

```http
Content-Security-Policy: default-src 'self'; img-src *; media-src media1.com media2.com; script-src userscripts.example.com
```
具体解释如下：
1. 图片可以从任何地方加载(注意 "*" 通配符)。
2. 多媒体文件仅允许从 media1.com 和 media2.com 加载(不允许从这些站点的子域名)。
3. 可运行脚本仅允许来自于userscripts.example.com。
### 示例 4
一个线上银行网站的管理者想要确保网站的所有内容都要通过SSL方式获取，以避免攻击者窃听用户发出的请求。

```http
Content-Security-Policy: default-src https://onlinebanking.jumbobank.com
```
该服务器仅允许通过HTTPS方式并仅从onlinebanking.jumbobank.com域名来访问文档。

