---
title: "CSS知识总结2"
date: 2020-05-09T14:44:23+08:00
draft: false
tags: ["CSS","前端"]
categories: ["CSS"]
---

# CSS 知识总结 2：CSS布局

感觉一篇太长了，感觉要分三篇，上一遍基本知识和历史，这一篇主要说布局，下一篇说渲染以及动画。

---
<!-- TOC -->

- [CSS 知识总结 2：CSS布局](#css-%e7%9f%a5%e8%af%86%e6%80%bb%e7%bb%93-2css%e5%b8%83%e5%b1%80)
  - [1. CSS布局分类](#1-css%e5%b8%83%e5%b1%80%e5%88%86%e7%b1%bb)
    - [1.1. 固定宽度布局](#11-%e5%9b%ba%e5%ae%9a%e5%ae%bd%e5%ba%a6%e5%b8%83%e5%b1%80)
    - [1.2. 不固定宽度布局](#12-%e4%b8%8d%e5%9b%ba%e5%ae%9a%e5%ae%bd%e5%ba%a6%e5%b8%83%e5%b1%80)
    - [1.3. 响应式布局:混合前两种](#13-%e5%93%8d%e5%ba%94%e5%bc%8f%e5%b8%83%e5%b1%80%e6%b7%b7%e5%90%88%e5%89%8d%e4%b8%a4%e7%a7%8d)
  - [2. CSS布局思路](#2-css%e5%b8%83%e5%b1%80%e6%80%9d%e8%b7%af)
    - [2.1. 从大到小](#21-%e4%bb%8e%e5%a4%a7%e5%88%b0%e5%b0%8f)
    - [2.2. 从小到大](#22-%e4%bb%8e%e5%b0%8f%e5%88%b0%e5%a4%a7)
  - [3. 三种主流布局](#3-%e4%b8%89%e7%a7%8d%e4%b8%bb%e6%b5%81%e5%b8%83%e5%b1%80)
    - [3.1. Float布局：](#31-float%e5%b8%83%e5%b1%80)
      - [3.1.1. 子元素：加float：left和width](#311-%e5%ad%90%e5%85%83%e7%b4%a0%e5%8a%a0floatleft%e5%92%8cwidth)
      - [3.1.2. 在父元素加上.clearfix](#312-%e5%9c%a8%e7%88%b6%e5%85%83%e7%b4%a0%e5%8a%a0%e4%b8%8aclearfix)
      - [3.1.3. 经验](#313-%e7%bb%8f%e9%aa%8c)
    - [3.2. Flex布局：flex flow弹性流](#32-flex%e5%b8%83%e5%b1%80flex-flow%e5%bc%b9%e6%80%a7%e6%b5%81)
      - [3.2.1. 容器container](#321-%e5%ae%b9%e5%99%a8container)
      - [3.2.2. item](#322-item)
      - [3.2.3. 经验](#323-%e7%bb%8f%e9%aa%8c)
    - [3.3. Grid布局](#33-grid%e5%b8%83%e5%b1%80)
      - [3.3.1. container](#331-container)
      - [3.3.2. items](#332-items)
  - [4.前端戒律](#4%e5%89%8d%e7%ab%af%e6%88%92%e5%be%8b)

<!-- /TOC -->

## 1. CSS布局分类

### 1.1. 固定宽度布局
1. 960
2. 1000
3. 1024
### 1.2. 不固定宽度布局
1. 手机上：依靠文档流
2. 文档流本身自适应，html本身就是响应式
### 1.3. 响应式布局:混合前两种
1. PC上固定宽度
2. 手机上不固定宽度

## 2. CSS布局思路
### 2.1. 从大到小
 1. 先定大局
 2. 完善各个部分的小布局
### 2.2. 从小到大
 1. 完成小布局
 2. 组合成大布局

## 3. 三种主流布局
 1. 兼容ie9：float布局(如果只有手机端，不用兼容ie9)
 2. flex布局
 3. gird布局
￼
### 3.1. Float布局：
#### 3.1.1. 子元素：加float：left和width
#### 3.1.2. 在父元素加上.clearfix
 1. .clearfix:after{content:'';display:block;clear:both;}
 2. 注意！！clearfix:after!!!!有after
#### 3.1.3. 经验
 1. 留一些空间；或最后一个不设置width，只设置最大
 2. Float布局，专门为ie准备，不能做响应式
 3. Ie6/7有bug，margin会双倍
     1. _margin=一半
     2. display：inline-block
 4. 图片上下有多余的
     1. Vertical-align:top;
     2. Vertical-align:middle;
 5. 块级元素&高度固定：居中
     1. margin: 0 auto;会覆盖
     2. Margin-left: auto;Margin-right: auto;不会覆盖
 6. 淘宝双飞翼：过时
 7. 加上头尾，满足所有pc需求，手机页面不用float做
 8. Float要自己计算宽度，不灵活
 9. IE一定成功，Chrome上成功，IE8不支持header，不支持::
￼
### 3.2. Flex布局：flex flow弹性流
#### 3.2.1. 容器container
  1. 设置成flex容器：display:flex/inline-flex;
  2. Item流动方向：
      1. flex-direction：row/column/row-reverse/column-reverse
      2. 是否换行flex-wrap：wrap/no wrap
          1. 不折行一直在同行挤下去
      3. 主轴对齐方式：默认主轴是横轴
          1. Justify-content:flex-start/flex-end/center/space-between/space-around/sapce-evenly;
          2. Bewteen:多余空间在容器之间
          3. Around:多余空间在周围，左右都有，等大，相比evenly双倍
          4. Evenly：多余空间在周围，且大小相等
      4. 次轴对齐方式，默认纵轴
          1. Align-items:flex-start/flex-end/center/stretch
          2. Stretch：和最长的一样高
      5. 多行内容：
          1. Align-content:flex-start/flex-end/center/space-between/space-around/space-evenly;
      6. 多行与主次轴区别
          1. 主次轴都是针对单行，items不同，行内的两个方向对齐
          2. 多行是行间的对齐方式
#### 3.2.2. item
  1. 选择器
      1. :first-child
      2. :nth-child(n)
  2. 顺序order：
      1. 越大越靠后，从小到大排列，负数同理
      2. 默认order是0
  3. 宽度
      1. Flex-grow：1
          1. 占一份，多余空间如何分配
          2. 默认0,能有多窄有多窄，防止变胖
      2. Flex-shrink:1
          1. 占一份，空间不够如何分配
          2. 0，按照width，防止变瘦
          3. 默认1
      3. flex-basic
          1. 默认auto
          2. 控制基准宽度，和width一样？
      4. Flex: 
          1. initial：0 1 auto
          2. auto自动：1 1 auto
          3. none非弹性：0 0 auto
      5. align-self：某一个item特殊对齐
#### 3.2.3. 经验
  1. 不要吧width和height写死
  2. 用min-width/max-width/min-height/max-height/width=xx vw(屏幕宽度)
  3. Flex配合margin-xxx:auto


### 3.3. Grid布局
#### 3.3.1. container
 1. Display:grid/inline-grid
 2. Grid-template-columns:40px 50px auto 40px;
 3. 也可以是1fr 2fr 
 4. Grid-template-area:”footer”
 5. Grid-gap
     1. Grid-column-gap:10px
     2. Grid-row-gap:10px
#### 3.3.2. items
1. Grid-row-start:0;
2. Grid-row-end:3;
3. Grid-column-start:0;
4. Grid-column-end:3;


## 4.前端戒律
必须先给设计稿，没有就自己画，让老板同意
1. 没有图不做
2. 两套界面，两套设计稿
3. 软件草图
    1. balsamiq
    2. figma在线版
    3. 墨刀
    4. adobe xd