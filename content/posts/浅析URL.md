---
title: "浅析URL"
date: 2020-05-11T12:21:45+08:00
draft: false
---

# 浅析URL
昨天先一口气把小后端搭起来了，用的阿里云服务器，然后node.js配的一个很简单的http响应。因为这部分之前已经比较了解了就先放了一下，现在总结一下。

---
<!-- TOC -->

- [浅析URL](#%e6%b5%85%e6%9e%90url)
  - [URL(Uniform Resource Locator)：统一资源定位服务](#urluniform-resource-locator%e7%bb%9f%e4%b8%80%e8%b5%84%e6%ba%90%e5%ae%9a%e4%bd%8d%e6%9c%8d%e5%8a%a1)
    - [URL的组成](#url%e7%9a%84%e7%bb%84%e6%88%90)
    - [URL的作用](#url%e7%9a%84%e4%bd%9c%e7%94%a8)
    - [域名 与 DNS](#%e5%9f%9f%e5%90%8d-%e4%b8%8e-dns)
    - [域名](#%e5%9f%9f%e5%90%8d)

<!-- /TOC -->
## URL(Uniform Resource Locator)：统一资源定位服务

### URL的组成

1. URL = 协议 + 域名 + 路径 + 查询参数 + 锚点 + （端口：默认 http80/https443）
2. 以本文的url为例
   1. 协议：http://
   2. 域名：yangyixuan.icu
   3. 路径：/posts/浅析url/
   4. 查询参数：唔这个我们还没有
   5. 锚点：#url的组成
   6. 端口：80，因为我们是http协议，所以默认80，可以在浏览器检查器里的request看到这个参数，应该是ip:80，如[39.107.252.247:8888/](http://39.107.252.247:8888/)，因为我的服务器没有备案，用80可能会被封，所以这里用了8888
3. 其中需要注意锚点不支持中文，且功能只是定位，只和浏览器有关，**不会发给服务器！！！**

### URL的作用

之前的介绍万维网WWW时提到:
>1990左右李爵士发明：为了上网冲浪，让每个人输入网址（URL），看到网页（HTML），看不见的就是HTTP
所以URL名字中已经很清楚，就是为了帮助我们定位网址和内容，但同样是网址，有的我们很容易记住，如[https://www.baidu.com/s?wd=hi](https://www.baidu.com/s?wd=yangyixuan.icu)，而有的像上面提到的[39.107.252.247:8888/hearts](http://39.107.252.247:8888/hearts)，则很难记住，其实可以发现，主要的差别就在域名的部分。说到域名就要提到DNS了。

### 域名 与 DNS 

DNS(Domain Name System)：域名系统，其实它的作用就是一张大表，帮我们记住不好记的域名。IP地址是互联网定位的基石，有了IP地址，我们就能够找到服务器，然后服务器再为我们返回我们需要的网页或数据，而DNS就为我们提供将url中的域名，转换为IP地址的服务。

用一个简单的例子看看DNS

    nslookup baidu.com
在终端输入这行命令你会看到
```
Server:		2408:8000:1010:2::8
Address:	2408:8000:1010:2::8#53

Non-authoritative answer:
Name:	baidu.com
Address: 220.181.38.148
Name:	baidu.com
Address: 39.156.69.79
```
啊哈，底下的两个Address就是IP地址，这下我们就知道如何向百度发送请求了。我们ping一下试试

```
ping baidu.com
```
可以看到，我们自动选择了一个IP，去尝试连接，并且成功。
```
PING baidu.com (220.181.38.148): 56 data bytes
64 bytes from 220.181.38.148: icmp_seq=0 ttl=50 time=7.258 ms
64 bytes from 220.181.38.148: icmp_seq=1 ttl=50 time=7.340 ms
64 bytes from 220.181.38.148: icmp_seq=2 ttl=50 time=13.424 ms
```

### 域名

最后，域名之间的区别是什么呢？
www.baidu.com 和 baidu.com 一样吗？

其实 .com是顶级域名，baidu.com是二级域名，www.baidu.com是三级域名，这也是DNS服务的方式——逐级解析。

我们先得到问解析.com的服务器(一般是网络运营商的服务器)baidu.com的ip存在哪儿，然后再到那里去问，最后得到www.baidu.com的ip地址，与该网站进行通信。

而这个过程又分为，递归式和迭代式，递归式，就像找个人问问题，他知道xxx会，就向xxx去学，学会了告诉你。而迭代式呢，还是你找他问问题，他说xxx会，让你自己找他去。这里面的具体细节我就不太了解了。

