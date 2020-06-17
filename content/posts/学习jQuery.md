---
title: "学习jQuery"
date: 2020-06-12T20:28:15+08:00
draft: false
tags: ["DOM", "前端"]
---

<!-- TOC -->

- [学习 jQuery](#学习-jquery)
  - [1. jQuery 如何获取元素](#1-jquery-如何获取元素)
  - [2. jQuery 的链式操作是怎样的](#2-jquery-的链式操作是怎样的)
  - [3. jQuery 如何创建元素](#3-jquery-如何创建元素)
  - [4. jQuery 如何移动元素](#4-jquery-如何移动元素)
  - [5. jQuery 如何修改元素的属性](#5-jquery-如何修改元素的属性)

<!-- /TOC -->

# 学习 jQuery

## 1. jQuery 如何获取元素

```
$(document) //选择整个文档对象
$('#xxx') //选择id为xxx的元素
$('.xxx') // 选择class为xxx的div元素
$('div').filter('.xxx'); //选择class等于xxx的div元素
```

## 2. jQuery 的链式操作是怎样的

jQuery 链式操作风格：使用全局函数获取对应元素，不返回元素，而是返回构造对象，这个对象可以用于操作对应元素。

这种风格使得所有操作可以连接在一起，以链条的形式写出来

```
jQuery('#xxx').find('.red').parent()
```

## 3. jQuery 如何创建元素

直接写 html 语句，jQuery 的构造函数就会自动生成对应元素

```
$('<p>Hello</p>');

$('<li class="new">new list item</li>');

$('ul').append('<li>list item</li>');
```

## 4. jQuery 如何移动元素

jQuery 提供两组方法来移动元素。一组方法是直接移动该元素，另一组方法是移动其他元素，使得目标元素达到我们想要的位置。

eg.将一个 div 元素，移动到 p 元素后面
直接法：把 div 元素移动 p 元素后面

```
$('div').insertAfter($('p'));
```

间接法：把 p 元素加到 div 元素前面

```
$('p').after($('div'));
```

这两组操作共有 4 对

1. 外前：.insertBefore()和.before()：在现存元素的外部，从前面插入元素
2. 外后：.insertAfter()和.after()：在现存元素的外部，从后面插入元素

3. 内前：.prependTo()和.prepend()：在现存元素的内部，从前面插入元素
4. 内后：.appendTo()和.append()：在现存元素的内部，从后面插入元素

## 5. jQuery 如何修改元素的属性

```

class 属性
.addClass('xxx')//添加指定类名
.removeClass('xxx')//删除指定类名

css属性
.css()//获取css或设置
.height()//获取高度或设置
.width()//获取宽度或设置

```

---

这周毕设答辩弄完了，哎学校学院还是太宠我们，真的觉得还没组会压力大呢,这两天再弄一个多路视频拼接的项目，昨天憋的难受，刚好出去小聚一下，北京又有新疫情，开学返校遥遥无期，最近还是有些懈怠，不可以呀，要努力呀呀呀
