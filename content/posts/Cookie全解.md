---
title: "Cookie全解"
date: 2022-04-04T19:13:28+08:00
draft: false
tags: ["JS","前端"]
categories: ["JS"]
---

# Cookie全解

>  TLDR：
> 
> 1. Cookie是服务器为了区分请求是否来自同一浏览器（记录用户状态）而发给浏览器的一段字符串，浏览器会将其保存在本地，在满足一些条件的情况下，在下次发送请求时带上这段字符串。
> 2. 后端在响应头中种下Cookie的方法
> ```javascript
> response.setHeader("Set-Cookie","key=value; Expires=date;HttpOnly;Secure");
> ```
> 3. 当前域的Cookie的Domain属性只能设置为【当前域名或者上级域名】，当前域只能设置或者读取 Domain 为【当前域名或者上级域名】的Cookie。
> ```javascript
> //当前域名为yangyixuan.icu
> //无效！无法设置Domain为子域名，第一个"."加不加均可，浏览器会默认加上
> response.setHeader("Set-Cookie","Domain=.test.yangyixuan.icu");
> //有效，设置当前域名为Domain，子域名可以访问并设置该cookie
> response.setHeader("Set-Cookie","Domain=.yangyixuan.icu");
> //有效，设置当前域名为Domain，子域名和其他.icu的二级域名都可以访问并设置该cookie
> response.setHeader("Set-Cookie","Domain=.icu");
> ```

<!-- TOC -->

- [Cookie全解](#cookie全解)
	- [1. Cookie](#1-cookie)
		- [1.1. 什么是Cookie？](#11-什么是cookie)
		- [1.2. Cookie设置方法和常用属性](#12-cookie设置方法和常用属性)
		- [1.3. Cookie作用域与非法域(设置限制）](#13-cookie作用域与非法域设置限制)
		- [1.4. Cookie注意事项](#14-cookie注意事项)
	- [2. Cookie与其他存储方式的区别](#2-cookie与其他存储方式的区别)
		- [2.1. Cookie与Session](#21-cookie与session)
		- [2.2. Web storage API： localStorage & sessionStorage](#22-web-storage-api-localstorage--sessionstorage)
	- [3. Cookie相关的安全问题](#3-cookie相关的安全问题)
		- [3.1. 跨站脚本攻击 XSS：Cross-site scripting](#31-跨站脚本攻击-xsscross-site-scripting)
		- [3.2. 会话攻击](#32-会话攻击)
		- [3.3. 跨站请求伪造 CSRF：Cross-site request forgery](#33-跨站请求伪造-csrfcross-site-request-forgery)
		- [3.4. 跟踪和隐私](#34-跟踪和隐私)

<!-- /TOC -->
## 1. Cookie 
### 1.1. 什么是Cookie？
Cookie是服务器发给浏览器的一段字符串，浏览器会将其保存在本地，在满足一些条件的情况下，在下次发送请求时带上这段字符串。

Cookie主要用于判断两个请求是否来自同一浏览器，使得无状态的HTTP协议也可以创建有状态的会话。

### 1.2. Cookie设置方法和常用属性
响应首部 Set-Cookie 被用来由服务器端向客户端发送 cookie。

建议只允许后端设置cookie，并且配合加密或Session，避免安全问题，详见[后文](#3-cookie相关的安全问题)
```javascript
//后端
response.setHeader("Set-Cookie","key=value; Expires=date;HttpOnly;Secure");

//前端
window.cookie = "key=value; Expires=date;HttpOnly;Secure"
//该语句会新增一个cookie，不会删除原有的cookie，应该是使用了代理
```
Cookie常用属性
```html
Set-Cookie:
	<!-- 必选参数 -->
	<cookie-name>=<cookie-value>;
	<!-- 其余均为可选参数 -->
	<!-- 过期定时 -->
	Expires=<date>;
	<!-- 过期倒计时 -->
	Max-Age=<non-zero-digit>;
	<!-- 主机名 -->
	Domain=<domain-value>;
	<!--  -->
	Path=<path-value>;
	Secure;
	HttpOnly;
	SameSite=Strict/Lax;
```
1. Expires： cookie 过期时间，形式为符合 [HTTP-date 规范](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Date)的时间戳。默认为会话期cookie，即浏览器被关闭时，该cookie会被移除。
2. Max-Age：经过多少秒数cookie会过期。**Max-Age 比 Expires 优先级更高。**
3. Domain：哪些Host（主机/域名）可以接受cookie。默认值为当前访问地址的Host，**注意：使用默认值时，不包含子域名，指定时一般默认包含子域名。**
4. Path：Host下的哪些路径可以接受cookie。**注意：目录的下级目录也满足匹配的条件（例如，如果 path=/docs，那么 "/docs" 和 "/docs/Web/HTTP" 都满足条件）。**
5. Secure：Cookie 只有在请求使用SSL和HTTPS协议的时候才会被发送到服务器。
6. HttpOnly：限制 Cookie 通过 JavaScript 经由  Document.cookie 属性、XMLHttpRequest 和  Request APIs 进行访问，以防范[跨站脚本攻击（XSS）](#32-会话劫持和xss)
7. [【新属性】](https://tools.ietf.org/html/draft-ietf-httpbis-rfc6265bis-05)SameSite：限制第三方Cookie与跨域请求一起发送，这样可以在一定程度上防范跨站[请求伪造攻击（CSRF）](#33-跨站请求伪造csrf)。默认是Lax，只允许导航到目标网址的GET请求，Strict完全禁止跨域时携带第三方Cookie。对于没有实现该属性的浏览器，其行为等同于 None，Cookies 会被包含在任何请求中。
8. cookie-name(Cookie 前缀)：对于支持 Cookie 前缀的浏览器来说，以 **__Secure-** 或 **__Host-** 为前缀的 Cookie，必须设置 Secure 属性，即必须在HTTPS安全连接中使设置cookie。并且，以 __Host- 为前缀的Cookie，path 属性的值必须为 "/" ，且不能含有 domain 属性（表示整个站点该cookie由该域独享，不与下级域名共享），否则无法成功设置Cookie。 而不支持 cookie 前缀的浏览器则并不检查相应的cookie属性是否合规，总是能成功设置cookie。
### 1.3. Cookie作用域与非法域(设置限制）
Cookie的作用域：Domain 和 Path 标识定义了Cookie的作用域，即该cookie允许哪些域名设置并访问。不考虑Path则可以总结为，当前域只能设置或者读取 Domain 为 ***「当前域名或者上级域名」*** 的Cookie。

Cookie的非法域：即当前域名可以设置哪些域名的Cookie。当前域名的Cookie的Domain属性只能设置为 ***「当前域名或者上级域名」***。
```javascript
//当前域名为yangyixuan.icu
//无效！无法设置Domain为子域名，第一个"."加不加均可，浏览器会默认加上
response.setHeader("Set-Cookie","Domain=.test.yangyixuan.icu");
//有效，设置当前域名为Domain，子域名可以访问并设置该cookie
response.setHeader("Set-Cookie","Domain=.yangyixuan.icu");
//有效，设置当前域名为Domain，子域名和其他.icu的二级域名都可以访问并设置该cookie
response.setHeader("Set-Cookie","Domain=.icu");
```


### 1.4. Cookie注意事项
1.  无论使用任何安全措施，敏感信息都不应该通过 Cookie 传输，因为 Cookie 本质上无法保证安全，可以访问客户端硬盘的人都可以读取Cookie。
2. 每当用户进行身份验证时，服务器端都应重新生成并重新发送会话 Cookie，甚至是已经存在的会话 Cookie。这样有助于防止[会话劫持](#33-会话固定攻击session-fixation-attacks)，在该攻击中第三方可以重用用户的会话。
3. 当指定domain时，实际上会在domain值前面加点（.），例如domain=yangyixuan.icu，事實上值是.yangyixuan.icu，那么cookie对yangyixuan.icu和其子域名（如二级域名.www.yangyixuan.icu）都是有效的，如果使用默认值，则不会加点，那么cookie只对当前域名生效。

## 2. Cookie与其他存储方式的区别 
### 2.1. Cookie与Session 
Cookie：Cookie是服务器为了标识用户而发给浏览器的一段字符串，浏览器会将其保存在本地，在满足一些条件的情况下，在下次发送请求时带上这段字符串。

Session：会话，是另一种记录用户状态的方式。浏览器访问服务器时，服务器通过某种方式标识用户，并把用户信息以某种形式记录在服务器上。Session一般通过Cookie来标识用户，即使用Cookie存储SessionID来实现的。
Cookie 和 Session的区别：
1. 存储位置：Cookie存在浏览器的文件里，Session存在服务器的文件里，过多用户信息会给服务器增加压力。
2. 大小：每个Cookie最大4k，每个域不超过20个cookie。Session的大小取决于服务器的性能和用户数，没有固定限制。
2. 有效期：Cookie有效期由maxAge和Expires决定，没有设置响应属性的cookie，在浏览器关闭时过期（目前浏览器有恢复模式，具体实现因浏览器而异），Session有效期由后端设置（Tomcat默认30分钟，php默认24分钟），以确保在用户没有使用系统一定时间后，自动退出登录，销毁session，一方面可以控制Session对服务器资源的占用，另一方面防止长期有效可能导致的安全问题。
3. 安全性：针对重放攻击，二者都不完全安全，一般Session仍需在客户端存储SessionID这个cookie，一旦攻击者获取到用户的所有cookie，均可利用该信息伪装成用户。但Session 设置了以后存储在服务器端，客户端看到的只是一个Session_id，这就相当于起到了一个隐藏或者说加密的作用，可以防止篡改攻击，即防止攻击者分析存放在本地的cookie，从而进行cookie伪造，实现越权攻击。
   


### 2.2. Web storage API： localStorage & sessionStorage
Cookie也曾用于客户端数据的存储，但由于Cookie大小和数量的限制，以及浏览器的每次请求都会携带 Cookie 数据，会带来额外的性能开销，Cookie 渐渐被淘汰，而Web storage API应运而生。 Web storage基于Storage 类型，该类型的对象用于保存名/值对数据	。

Web Storage 包含如下两种机制：
1. sessionStorage 为每一个给定的源origin维持一个独立的存储区域，该存储区域在页面会话期间可用（即只要页面处于打开状态，包括关闭页面后的重新加载和恢复）。
2. localStorage 同样的功能，但是在浏览器关闭，然后重新打开后数据仍然存在。
```javascript
// 使用方法存储数据 
sessionStorage.setItem("name", "Howard");
// 使用属性存储数据
sessionStorage.website = "yangyixuan.icu";
// 使用方法取得数据
let name = sessionStorage.getItem("name");
// 使用属性取得数据
let book = sessionStorage.website;
```
注意：
1. Storage 类型只能存储字符串。非字符串数据在存储之前会自动转换为字符串。
2. 对于每个源（origin）sessionStorage 和 localStorage 使用不同的 Storage 对象独立运行和控制。
3. 浏览器限制每个源的存储大小将限制在10M。

## 3. Cookie相关的安全问题 
### 3.1. 跨站脚本攻击 XSS：Cross-site scripting
跨站脚本攻击（Cross-site scripting，XSS）是一种安全漏洞，攻击者可以利用这种漏洞在网站上注入恶意的客户端代码。

当被攻击者登陆网站时就会自动运行这些恶意代码，从而将 cookies 或其他隐私信息（如sessionID）发送给攻击者，攻击者可以突破网站的访问权限，冒充受害者。

设置HttpOnly属性可以防止Cookie 通过 JavaScript 经由 Document.cookie 属性、XMLHttpRequest 和 Request APIs 进行访问后泄漏。

XSS更多细节，详见[XSS实战](https://yangyixuan.icu/posts/XSS%E5%AE%9E%E6%88%98/)
### 3.2. 会话攻击
在Session会话中，为了维持来自同一个用户的不同请求之间的状态，客户端必须要给服务器端发送一个唯一的身份标识符(Session ID)点， 而这就给了攻击者可乘之机。

通过session的攻击有很多种方式。大部分的手段都是首先通过捕获合法用户的session, 然后冒充该用户来访问系统，进而对系统进行攻击，而攻击者首先要做的就是至少必须要获取到一个有效的sessionID，用于接下来的身份验证。　

攻击者有以下两种攻击方式，获取一个有效的sessionID:
1. 会话劫持 session  Hijack
会话劫持是通过在用户正常登录并获取SessionID后，通过某种攻击手段，对其进行窃取，从而获得一个有效的SessionID

2. 会话固定 session fixation
会话固定是一种诱骗受害者使用攻击者指定的会话标识（SessionID）的攻击手段。该方法让合法用户使用黑客预先设置的sessionID进行登录，从而是Web不再进行生成新的sessionID，从而导致黑客设置的sessionId变成了合法有效的SessionID。

除了设置HttpOnly头部外，使用User-Agent验证，Token校验，并且采用 每当用户登陆的时候就进行重置sessionID以及sessionID闲置过久时，进行重置sessionID等策略，也可以避免会话攻击。

### 3.3. 跨站请求伪造 CSRF：Cross-site request forgery
CSRF跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

一个典型的CSRF攻击有着如下的流程：
1. 受害者登录a.com，并保留了登录凭证（Cookie）。
攻击者引诱受害者访问了b.com。
2. b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带a.com的Cookie。
3. a.com接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。
4. a.com以受害者的名义执行了act=xx。
5. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让a.com执行了自己定义的操作。

后端做同源检测，并设置Cookie的Samesite属性可以预防这种攻击。

### 3.4. 跟踪和隐私 
第一方Cookie vs 第三方Cookie：cookie的域与当前页面的域相同，则该 cookie 称为第一方 cookie（ first-party cookie）。如果域不同，则它是第三方 cookie（third-party cookie）。

当当前网页的服务器设置第一方 Cookie 时，该页面也可能包含存储在其他域中的服务器上的图像或其他组件（例如，广告横幅），这些图像或其他组件可能会设置第三方 Cookie。这些主要用于在网络上进行广告和跟踪。例如，谷歌广告。

第三方服务器可以基于同一浏览器在访问多个站点时发送给它的 cookie 来建立用户浏览历史和习惯的配置文件。但同时也可能导致用户隐私泄漏，因此是否同意使用第三方Cookie也是安全和便利之间的一个取舍，我们应该意识到这种便利的代价和背后的安全隐患。

<hr>
终于把Cookie好好梳理了一遍。终于算是心中有数了，实在不易，继续加油！