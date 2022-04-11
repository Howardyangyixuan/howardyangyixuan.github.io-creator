---
title: "理解CSRF"
date: 2022-04-11T16:15:54+08:00
draft: false
---

# 理解CSRF
<!-- TOC -->

- [理解CSRF](#理解csrf)
	- [什么是CSRF](#什么是csrf)
	- [与XSS攻击的区别](#与xss攻击的区别)
	- [防护策略](#防护策略)
		- [1. 拒绝不安全的跨域访问](#1-拒绝不安全的跨域访问)
			- [同源检测](#同源检测)
			- [设置Cookie的Samesite属性](#设置cookie的samesite属性)
		- [2. 附加第三方无法获取的凭证](#2-附加第三方无法获取的凭证)
			- [CSRF Token](#csrf-token)
			- [双重Cookie验证](#双重cookie验证)

<!-- /TOC -->
## 什么是CSRF
CSRF（Cross-site request forgery）跨站请求伪造是一种利用受害者在目标网站已获取的凭证（如Cookie），伪造请求冒充受害者在目标网站进行恶意操作的攻击方式。

> [美团前端安全系列之二：如何防止CSRF攻击？](https://segmentfault.com/a/1190000016659945)
> - 一个典型的CSRF攻击有着如下的流程：
> 1. 受害者登录a.com，并保留了登录凭证（Cookie）。
> 2. 攻击者引诱受害者访问了b.com。
> 3. b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带a.com的Cookie。
> 4. a.com接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。
> 5. a.com以受害者的名义执行了act=xx。
> 6. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让a.com执行了自己定义的操作。

## 与XSS攻击的区别
XSS特点：
1. 发生在目标网站
2. 直接尝试获取凭证（Cookie）

CSRF特点
1. 触发在第三方网站
2. 无法直接获取凭证，间接利用凭证

## 防护策略
### 1. 拒绝不安全的跨域访问
针对CSRF触发在第三方的特点，可以通过拒绝不安全的跨域访问来防御CSRF攻击。主要方法有：
1. 同源检测
2. 设置Cookie的Samesite属性

#### 同源检测
同源策略主要对HTTP请求头的Origin和Referer字段进行核查，仅允许白名单上的域名。
> Origin: 
> 1. 定义：表示发起请求的域名，其值不包含path及query。
> 2. 何时包含该字段：浏览器会将Origin请求头添加到所有跨域的请求中，除GET或HEAD请求外的同源请求。
> 
> Referer:
> 1. 定义：表示请求的来源地址，即表示当前页面是通过此来源页面里的链接进入的。
> 2. 何时包含该字段：取决于Referrer-Policy字段。 该字段有三种方法设置 1.[CSP](http://yangyixuan.icu/posts/%E6%B5%85%E6%9E%90csp) 2.页面头部meta标签 3.a标签增加referrerpolicy属性 
> ```http
> Referrer-Policy: no-referrer
> - 整个 Referer  首部会被移除。访问来源信息不随着请求一起发送。
> Referrer-Policy: no-referrer-when-downgrade
> - [默认值]在没有指定任何策略的情况下用户代理的默认行为。在同等安全级别的情况下，引用页面的地址会被发送(HTTPS->HTTPS)，但是在降级的情况下不会被发送 (HTTPS->HTTP)。
> Referrer-Policy: origin
> - 在任何情况下，仅发送文件的源作为引用地址。
> - 例如 http://a.com/csrf.html 会将 http://a.com/ 作为引用地址。
> Referrer-Policy: origin-when-cross-origin
> - 对于同源的请求，会发送完整的URL作为引用地址，但是对于非同源请求仅发送文件的源。
> Referrer-Policy: same-origin
> - 对于同源的请求会发送引用地址，但是对于非同源请求则不发送引用地址信息。
> Referrer-Policy: strict-origin
> - 在同等安全级别的情况下，发送文件的源作为引用地址(HTTPS->HTTPS)，但是在降级的情况下不会发送 (HTTPS->HTTP)。
> Referrer-Policy: strict-origin-when-cross-origin
> - 对于同源的请求，会发送完整的URL作为引用地址；在同等安全级别的情况下，发送文件的源作为引用地址(HTTPS->HTTPS)；在降级的情况下不发送此首部 (HTTPS->HTTP)。
> Referrer-Policy: unsafe-url
> - 无论是同源请求还是非同源请求，都发送完整的 URL（移除参数信息之后）作为引用地址。
> ```

#### 设置Cookie的Samesite属性
Samesite的默认值是Lax，只允许导航到目标网址的GET请求携带Cookie，而Strict完全禁止跨域时携带第三方Cookie(与当前页面的域不同的Cookie)。对于没有实现该属性的浏览器，其行为等同于 None，Cookies 会被包含在任何请求中。

### 2. 附加第三方无法获取的凭证
针对CSRF无法直接获取凭证的特点，可以通过附加第三方无法获取的凭证，以区分真实用户和攻击者
1. CSRF Token
2. 双重Cookie验证

#### CSRF Token

CSRF Token的主要思路就是在用户获取凭证时，服务端将一个附加的、第三方无法获取的凭证发放给用户，当用户再次发送请求时，将该凭证添加在请求中，以区分用户和攻击者。

下面具体介绍CSRF Token的使用方法：
1. 服务端发放CSRF Token
   1. 用户首次打开页面时，服务器创建会话，同时通过时间戳+随机字符串给用户生成一个Token，加密后得到CSRF Token将其放入Session中保存用于后续比对。
   2. 在之后每次页面加载时，使用JS遍历整个DOM树，对于DOM中所有的a和form标签后加入CSRF Token，以便再次发送请求时携带CSRF Token。
      1. 对于a标签（GET请求），CSRF Token将通过URL参数传递，形如 http://yangyixuan?csrftoken=tokenvalue。 
      2. 对于form表单（POST请求），在 form 表单的最后加上： 
      ```html 
      <input type=”hidden” name=”csrftoken” value=”tokenvalue”/>
      ```
2. 用户再次发送请求时，携带CSRF Token
   1. 页面中已经嵌入CSRF Token的a标签和form表单无需额外操作，触发时会自动携带。
   2. 动态生成的内容需手动解析并将CSRF Token加入请求中
3. 服务器验证CSRF Token
服务器收到用户的请求后，解密CSRF Token，对比随机字符串和时间戳，如果字符串一致且时间戳超过有效期限，那么CSRF Token有效，该用户通过验证。

存在的问题
1. 要求没有XSS漏洞：虽然CSRF Token能够有效预防CSRF攻击，但如果存在XSS漏洞，造成Token泄漏，CSRF攻击依然可以奏效。
2. 实现较为复杂，容易出现遗漏：需要给每一个页面都写入Token（前端无法使用纯静态页面），每一个Form及Ajax请求都携带这个Token，后端对每一个接口都添加对应的输出和校验，并保证各个页面Token及请求Token一致。
3. 性能问题： 由于使用Session存储，读取和验证CSRF Token会引起比较大的复杂度和性能问题。（可以使用Encrypted Token Pattern方法缓解，该方法通过计算，而非生成的方式得到Token，在校验时无需再去读取，只需再次计算一次即可）

#### 双重Cookie验证
在Session中存储CSRF Token比较繁琐，而且不能在通用的拦截上统一处理所有的接口，而双重Cookie验证基本解决了这一问题，并且缓解了性能开销。

核心思想仍是第三方无法获取Cookie内容，可以要求Ajax和表单请求携带一个Cookie中的值，从而区分用户和攻击者。

双重Cookie验证的具体流程如下：
1. 在用户访问网站页面时，向请求域名注入一个Cookie，内容为随机字符串（例如csrfcookie=v8g9e4ksfhw）。
在前端向后端发起请求时，取出Cookie，并添加到URL的参数中（接上例POST https://www.a.com/comment?csrfcookie=v8g9e4ksfhw）。
2. 后端接口验证Cookie中的字段与URL参数中的字段是否一致，不一致则拒绝。

存在的问题：
1. 要求没有XSS漏洞：如果攻击者可以注入Cookie，那么该防御方式失效。
2. 难以做到子域名的隔离。


<hr>
安全篇暂时告一段落，学到了很多以前没有注意到的安全问题，今后要更加注意并防止打开可以的链接，避免各类攻击的发生。
