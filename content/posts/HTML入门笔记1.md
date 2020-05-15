---
title: "HTML入门笔记1"
date: 2020-05-04T15:14:11+08:00
draft: false
tags: ["HTML","前端"]
categories: ["HTML"]
---

# HTML 入门笔记 1

> 写在前面：
>
> “Hugo 是垃圾，请用平台！”
>
> 有点服方方了！！！
>
> 确实把它征服了之后，不要再管它了，hugo 就是垃圾，麻烦，没有留言和分享，教我们用只是让我们爽一下，有成就感，用命令行爽一下，仅此而已。

> 学习思路之一：教大家学很多东西，会了之后，不要满足于学会这个！工匠不会对起子有崇拜，理发师不会对剪刀崇拜，不要对工具有崇拜之情！可以分析好处，但不要崇拜！

> 虽然不建议长期使用，但是很多基础的东西并且不太需要给别人看的在这里写写也蛮好的，总是一种成长的记录嘛。一直觉得活的很糙，也算给自己一种仪式感。下面进入正文：

## 1.HTML 的诞生

### 1.1. 90 年代的网络

因特网的雏形（ARPANET）在 1960 年左右，即冷战时期诞生，随后逐渐从军事、科研、商用，走进了人们的生活，但是 1990 年之前人们对网络的使用依旧非常淳朴：

1. 电话拨号上网，20kb/s
2. 收发邮件
3. IRC（在 Redhat 实习时有幸接触并使用哈哈哈）
4. 基于 Telnet 的 BBS

似乎与我们所熟知的网络还相差甚远。

### 1.2 李爵士开创万维网

> 因为 TimBerners-Lee 后来被授予了爵位所以我们称他李爵士

TimBerners-Lee 为了能够上网冲浪（WWW），他构想了一个奇妙的过程。每个人只需输入网址（URL），就可看到网页（HTML），但是其中需要一些操作，那么让另外一些程序来做吧（HTTP）。

思路清晰了，那么开始：

1. 我写一个浏览器
2. 我搭一个服务器
3. 自己的浏览器访问自己的服务器

而在实现这个构想的过程中，李爵士就发明了 WWW，同时发明了 URL + HTTP + HTML

大致了解了 HTML 的起源，以及它实质就是网页的表达形式，那么我们开始学习它吧，其实因为这也是一种语言，所以这些东西像背单词，在语境里才能真正理解并会使用，这里只是简单梳理一下，用于以后查询。

## 2.入门 HTML

### 2.1. HTML 起手应该写什么

Html 起手式

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body></body>
</html>
```

这段代码是 html 开篇必写的内容

1. 指定文档类型：浏览器支持多种语言，如 svg，xml

```html
<!DOCTYPE html>
```

2. 文档语言：

```html
<html lang="en"></html>
```

3. Head 和 body 一般不缩进：很少改变这个结构，省空间

   1. Head 写看不见的元素

4. 编码
   1. UTF-8 全人类所有语言，使用 UTF-8！
   2. GTK 亚洲语言

```html
<meta charset="UTF-8" />
```

5. meta：
   1. name=“viewport”视窗，防止页面缩放

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

6. 可能有 ie 的一行代码
   1. 控制 ie 的版本
   2. ie=edge 用最新版本
7. 顺序一般无关
   1. 加载 css 和 js 时才有关
   2. Utf-8 写在最前面

### 2.2. 常用 HTML 标签

此处只是做记录用，不再具体解释。

#### 2.2.1 常用章节标签

1. 标题 h1-h6
   1. 一般一篇文章只有一个 h1
2. Section 章节
   1. 里面有 h2-6
3. 文章 article
4. 段落 p
5. 头部 header：做广告
6. 脚部 footer：做版权、广告
7. 主要内容 main
8. 旁支内容 aside：导航、参考资料，
   1. 不是主要内容
9. 划分 div
   1. 希望 main 和 aside 是一个整体

#### 2.2.2 常用属性标签

全局属性：所有标签都有的属性

1. class
   1. 一个标记：用于分类
   2. 就是一个标记
   3. [class=xxx]{}只能完全匹配
   4. .xxx{}可以部分匹配
2. contenteditable
   1. 使得任何元素可被编辑
   2. 可以做自己的编辑器
3. 隐藏 hidden
   1. 快速让一个东西看不见
   2. Css 可以让他重新显示
4. id
   1. Css 相关
      1. 和 class 区别
         1. 传统说法：全页面唯一 id，否则用 class，但是 id 没有唯一性，会误导别人
         2. 不到万不得已，不要用 id
         3. 因为 id 不报错！即使重复！
      2. [id=xxx]{}
      3. #xxx{}
   2. Js 相关
      1. 可以直接获取 id
      2. xxx.style.border = '1px solid green'
      3. Id 不为一时，会报错！
      4. 同样，不到万不得已，不要用
      5. 因为有一些忌讳，这些关键词不能使用，
         1. window 的全局属性
            1. parent、self、top
            2. 可以写，但 js 不能找到
5. style
   1. 优先级高于 css，低于 js，因为 js 会覆盖
   2. 能否被看见？
      1. Style 在 body 中，<stytle>style{display:block; border:1px solid red}</stye>即可看到
      2. 配合 contentedible 可以辅助调试
6. tabindex：用得较少
   1. 可交互的部分都可以用 tab 访问
      1. 有的人没有鼠标
   2. 是 tab 访问的序号
      1. 正的顺序访问
      2. 0 是最后一个
      3. -1 永远找不到（负值都找不到）
7. Title：显示完整的内容
   1. 单行文字溢出：{white-space:nowrap;text-overflow:ellipsis;overflow:hidden;}
