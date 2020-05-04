---
title: "如何用hugo搭建个人博客"
date: 2020-05-03T15:02:39+08:00
draft: false
---

# 如何用 hugo 搭建个人博客

## 1.什么是 Hugo

Hugo 是最快的博客生成器，由 Go 语言实现。

另外一个常用的生成器是 Hexo
由 Js 实现，方方老师说有点难用，有空自己尝试下。

> 前段时间看 Joint Embedding Space 和 PointNet 等论文时，有几个博客写的很好，令我茅塞顿开，而且用的都是同一个简约大方的主题，主题是[Next](https://theme-next.org/)

## 2.Hugo 教程

参照 Hugo 快速开始教程即可快速上手

> [Hugo 官方教程](https://gohugo.io/getting-started/quick-start)

下面主要梳理一下每一步的逻辑和对应方法

### 2.1. 建立本地站点生成器

#### 2.1.1. 创建一个空目录

    hugo new site howardyangyixuan.github.io-creator

    cd howardyangyixuan.github.io-creator

注意：一定要 cd 进去！！！

    git init

#### 2.1.2. 建立并配置站点（即博客生成器）

按说明[Hugo 官方教程](https://gohugo.io/getting-started/quick-start)和命令行提示载入主题 theme，下载很慢，最好给命令行加代理。

这一步就是配置生成器要输出的格式，各个主题就是给生成器参考的配置文件，这部完成以后就可以开始写文章，并让生成器生成博文啦！

#### 2.1.3. 撰写文章&预览

1.新建文章：

    hugo new posts/xxx.md

2.在本地 server 上查看（预览生成的文章）：

    hugo server -D

3.生成博文到 Public（Public 中的文件后续要发布的内容）：

hugo
hugo -D

### 2.2. 建立万维网（WWW）站点

本地的内容完成一后，我们如何让其他人看到我们的站点呢？

核心思想就是用一个服务器存储我们的本地内容，当其他人想访问时，服务器来处理他们的请求，并让我们的页面传送给他们。

自己搭建服务器不够方便，所以像 Github 就提供了托管的服务，Github Pages，我们只需建立一个与之对应的 Github 仓库即可交由 Github 来处理。

下面是具体方法：

#### 2.2.1. Github 中创建与 github id 同名的仓库！

形如 xxx.github.io

例如我的就是 howardyangyixuan.github.io

然后将 public 推送到 GitHub

注意：是在 public 目录下操作

    git remote add origin xxx.git
    git push -u origin master

#### 2.2.2 配置 Github Pages

- settings：GIthub Pages
- 如果需要选，选 master

## 至此，个人博客开通成功！

通过 howardyangyixuan.github.io 即可查看到我们的博客啦！

### 2.3. 购买并配置个人域名

howardyangyixuan.github.io 看上去不太好记，所以我们可以购买个人域名。
.com 这种顶级域名都被抢注了，哪个好记还便宜我就买哪个了。

.love 的域名也挺吸引人的 hhh 我想买个 hjx 啥的哈哈哈哈

这一部分网上教程就比较多了，不细说了～

最后其实我纠结了一小下主题啥的，不过还是好好写内容吧，我最近都是在 mac 备忘录里记得，挺好的，有 icloud，手机上 ipad 上也很方便，博客上还是主要放一些比较困难的想要分享的读书或者学习笔记吧，这种笔记感觉意义不是特别大，自己在备忘录里偶尔查一下就好了。

啊啊啊不说了，还有好多事儿要做！加油呀 hhhh！！
2020.5.3
