---
title: "CSS知识总结 1"
date: 2020-05-08T13:33:42+08:00
draft: false
tags: ["CSS","前端"]
categories: ["CSS"]
---

# CSS 知识总结 1

最近快速的把css过去了，感觉css本身不正交，细节太多，知识框架大概有了，基本的功能了解了会用了，然后做了几个小练习就得赶快继续，没有必要纠结太久，之后在项目里不断积累就好。

---
<!-- TOC -->

- [CSS 知识总结 1](#css-%e7%9f%a5%e8%af%86%e6%80%bb%e7%bb%93-1)
  - [1. CSS 小常识](#1-css-%e5%b0%8f%e5%b8%b8%e8%af%86)
    - [1.1. 层叠的含义](#11-%e5%b1%82%e5%8f%a0%e7%9a%84%e5%90%ab%e4%b9%89)
    - [1.2. CSS版本](#12-css%e7%89%88%e6%9c%ac)
    - [1.3. 浏览器支持](#13-%e6%b5%8f%e8%a7%88%e5%99%a8%e6%94%af%e6%8c%81)
  - [2. CSS基本概念](#2-css%e5%9f%ba%e6%9c%ac%e6%a6%82%e5%bf%b5)
    - [2.1. 文档流Normal Flow：](#21-%e6%96%87%e6%a1%a3%e6%b5%81normal-flow)
      - [2.1.1. 流动方向：](#211-%e6%b5%81%e5%8a%a8%e6%96%b9%e5%90%91)
      - [2.1.2. 宽度：](#212-%e5%ae%bd%e5%ba%a6)
      - [2.1.3. 高度](#213-%e9%ab%98%e5%ba%a6)
      - [2.1.4. overflow溢出：设置的比有的低](#214-overflow%e6%ba%a2%e5%87%ba%e8%ae%be%e7%bd%ae%e7%9a%84%e6%af%94%e6%9c%89%e7%9a%84%e4%bd%8e)
    - [2.2. 盒模型](#22-%e7%9b%92%e6%a8%a1%e5%9e%8b)
    - [2.3. 基本单位](#23-%e5%9f%ba%e6%9c%ac%e5%8d%95%e4%bd%8d)

<!-- /TOC -->
## 1. CSS 小常识
CSS：层叠样式表
### 1.1. 层叠的含义
  1. 样式：多次对用一选择器，进行样式声明
  2. 选择器：不同选择器对同一元素，进行样式声明
  3. 文件：多个文件进行层叠，即多个CSS文件
  4. 极度灵活，也是隐患，会被别人覆盖

### 1.2. CSS版本
CSS2.1 最广泛使用
CSS3 现代版本，开始分模块，自此每个模块自己升级，只有模块版本号，没有整体的CSS版本

### 1.3. 浏览器支持

前端社区Can I Use：caniuse.com

## 2. CSS基本概念

### 2.1. 文档流Normal Flow：

#### 2.1.1. 流动方向：
 1. inline内联元素：如span元素，从左到右，到最右换行，会分为两部分
 2. block块元素：如div元素，从上到下,每个一行
 3. inline-block元素：从左到右
     1. 不会在两行间
 4. 每个元素都可以是内联或者块级元素
     1. 浏览器默认样式inline，如没有被包住的文字
     2. display:inline/block/inline-block
#### 2.1.2. 宽度：
 1. 内联inline元素：尽量窄
     1. 如span由所有内联宽度组成
     2. 内联元素**不接受宽度**，不能用width指定
     3. 不要在inline元素里写block元素
 2. 块级block元素：尽量宽
     1. Div能有多宽有多宽，不是100%，默认是auto
     2. Div宽度不要写100%
     3. 宽度可由width指定
 3. inline-block元素：尽量窄
     1. 可以设置宽度
#### 2.1.3. 高度
 1. inline元素
     1. span高度由line-height实际高度间接决定
         1. 字体会改变！
             1. 参见：方 line height
             2. 行盒
     2. 内联元素**不接受高度**，height不能设置
     3. padding改变可视范围，不改变高度
     4. span无内容：高度由line-height决定
 2. block元素
     1. 里面的文档流元素确定，全部包住
     2. 有些元素可以脱离文档流
         1. absolute
     3. 设置height
     4. div无内容：高度为0
 3. inline-block
     1. 里面的文档流元素确定，全部包住
     2. 可设置block
#### 2.1.4. overflow溢出：设置的比有的低

1. overflow：
  1. visible
  2. hidden
  3. Scroll：会一直显示滚动条
  4. Auto：根据内容开启/关闭滚动条
      1. 内联元素只显示在第一屏
2. overflow-x/overflow-y不是很好用
  1. 两个方向的滚动条
### 2.2. 盒模型
1. 基本组件
    1. 内容content
    2. 内边距padding
    3. 边框border
    4. 外边距marign
2. 两种盒模型
    1. content-box：width\height只包含content
    2. border-box：width\height包含content+padding+border
    3. border-box好用
3. margin合并：只上下合并
    1. 两个孩子间margin合并
        1. inline-block可以取消margin合并
    2. Parent与第一个上和最后一个下的margin合并
        1. 取消
            1. 之间加padding或border
            2. overflow

### 2.3. 基本单位
1. px像素
2. em自身font-size的倍数
3. 百分数
4. 整数

