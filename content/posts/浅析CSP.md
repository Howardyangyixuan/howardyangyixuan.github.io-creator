---
title: "浅析CSP"
date: 2022-04-10T09:59:38+08:00
draft: false
---

# 浅析CSP
> 上文[XSS实战](http://yangyixuan.icu/posts/xss%E5%AE%9E%E6%88%98/)我们提到CSP是内容安全策略是一种系统性预防XSS攻击的手段，下面就来具体了解一下CSP以及具体如何设置CSP

## 什么是CSP
CSP内容安全策略(Content Security Policy)是通过白名单过滤的方式，用于预防各种跨站攻击和数据注入攻击的一种安全策略。

具体而言，CSP配置限制浏览器获取页面资源的来源，如一个可以上传文件和显示图片页面，应该允许图片来自任何地方，但限制表单的action属性只可以赋值为指定的站点。
## CSP的设置方法和常用属性
### CSP的设置方法
> ```javascript
> response.setHeader("Content-Security-Policy","具体policy");
> ```

### Cookie常用属性
```html
Content-Security-Policy:
	<!-- 均为可选参数 -->
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
	<!--  -->
```
源 ``<source>``
   1. ``<host-source>``： 以域名或者 IP 地址表示的主机名，外加可选的 URL 协议名（URL scheme）以及端口号。站点地址中可能会包含一个可选的前置通配符（星号 '\*'），同时也可以将通配符（也是'\*'）应用于端口号，表示在这个源中可以使用任意合法的端口号。 
   
	 举例说明： 
	 http://*.example.com: 匹配从使用 http: 的 example.com 的任意子域的资源加载。 

	 mail.example.com:443:匹配对 mail.example.com 上的 443 端口号的访问。

	 https://store.example.com: 匹配对使用了 https: 的 store.example.com 的访问。
   3. ``<scheme-source>``：协议名如'http:' 或者 'https:'。必须带有冒号，不要有单引号。同时你还可以指定数据协议（data schema）（不推荐使用）。
      1. data: 允许 data: URIs 作为内容的源。这是不安全的。攻击者可以注入任意 data: URI 。不要轻易使用这种形式的源，尤其是脚本，绝对不要使用。
      2. mediastream: 允许 mediastream: URIs 作为内容的源。
      3. blob: 允许 blob: URIs 作为内容的源。
      4. filesystem: 允许 filesystem: URIs 作为内容的源。
   4. 'self'：指向与要保护的文件所在的源，包括相同的 URL scheme 与端口号。必须有单引号。一些浏览器会特意排除 blob 与 filesystem 。需要设定这两种内容类型的站点可以在 Data 属性中进行设定。
   5. 'unsafe-inline'： 允许使用内联资源，例如内联 ``<script>``  元素（javascript: URL）、内联事件处理器以及内联 ``<style>`` 元素。必须有单引号。
   6. 'unsafe-eval'：允许使用 eval() 以及相似的函数来从字符串创建代码。必须有单引号。 
   7. 'none'：不允许任何内容。 必须有单引号。
   8. 'nonce-<base64值>'：特定使用一次性加密内联脚本的白名单。服务器必须在每一次传输政策时生成唯一的一次性值。否则将存在绕过资源政策的可能。请参见不安全的内联脚本查看示例。 
   9. ``<hash-source>``： 使用 sha256、sha384 或 sha512 编码过的内联脚本或样式。其由用短划线分隔的两部分组成: 用于创建哈希的加密算法, 以及脚本或样式base64编码的哈希值。当生成哈希值的时候，不要包含 ``<script>`` 或 ``<style>`` 标签，同时注意字母大小写与空格——包括首尾空格——都是会影响生成的结果的。请参见不安全的内联脚本。
   10. 'strict-dynamic'：strict-dynamic 指定对于含有标记脚本(通过附加一个随机数或散列)的信任，应该传播到由该脚本加载的所有脚本。与此同时，任何白名单以及源表达式例如 'self'  或者  'unsafe-inline' 都会被忽略。参见 script-src 。

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
在这里，各种内容默认仅允许从文档所在的源获取, 但存在如下例外:

图片可以从任何地方加载(注意 "*" 通配符)。
多媒体文件仅允许从 media1.com 和 media2.com 加载(不允许从这些站点的子域名)。
可运行脚本仅允许来自于userscripts.example.com。
### 示例 4
一个线上银行网站的管理者想要确保网站的所有内容都要通过SSL方式获取，以避免攻击者窃听用户发出的请求。

```http
Content-Security-Policy: default-src https://onlinebanking.jumbobank.com
```
该服务器仅允许通过HTTPS方式并仅从onlinebanking.jumbobank.com域名来访问文档。

### 示例 5
一个在线邮箱的管理者想要允许在邮件里包含HTML，同样图片允许从任何地方加载，但不允许JavaScript或者其他潜在的危险内容(从任意位置加载)。
```http
Content-Security-Policy: default-src 'self' *.mailsite.com; img-src *
```
注意这个示例并未指定script-src (en-US)。在此CSP示例中，站点通过 default-src 指令的对其进行配置，这也同样意味着脚本文件仅允许从原始服务器获取。
