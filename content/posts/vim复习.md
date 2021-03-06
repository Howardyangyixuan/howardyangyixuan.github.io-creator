---
title: "Vim复习"
date: 2020-07-13T22:28:57+08:00
draft: false
tags: ["开发工具"]
categories: ["开发工具"]
---

# Vim 复习

第一次学习 Vim 大约是在两年前，刚刚接触 Linux，必须使用 vim 进行文本编辑，当时觉得挺反人类的，但随着代码量的增加，产生了对编码速度的追求，因此开始复习 vim 并尝试在编码中逐渐练习并长期使用

<!-- TOC -->

- [Vim 复习](#vim-复习)
  - [1. 导言](#1-导言)
  - [2. 定位](#2-定位)
    - [2.1. 单词定位](#21-单词定位)
    - [2.2. 行定位](#22-行定位)
    - [2.3. 段落定位](#23-段落定位)
    - [2.4. 屏幕定位](#24-屏幕定位)
    - [2.5. 文件定位](#25-文件定位)
  - [3. 增](#3-增)
  - [4. 撤销&重做](#4-撤销重做)
  - [5. 删](#5-删)
  - [6. 改](#6-改)
  - [7. 查](#7-查)
    - [7.1 行内查找字母](#71-行内查找字母)
    - [7.2 文件内查找单词](#72-文件内查找单词)
  - [8. 复制粘贴](#8-复制粘贴)

<!-- /TOC -->

## 1. 导言

所有编码操作无非增删改查，首先推荐一个[Vim 基础及在线练习](https://code.snipcademy.com/tutorials/vim)，Youtube 上有配套视频，不过不知道为啥点击量很少。

## 2. 定位

### 2.1. 单词定位

- 下个词头：W 忽视标点，w 不忽视标点
- 下个词尾：E，e
- 上个词头：B, b

### 2.2. 行定位

- 行首：0
- 行尾：\$
- 非空白行首：^
- 非空白行行尾：g\_

### 2.3. 段落定位

- 上一段落：(
- 下一段落：)
- 非空白上一段落：{
- 非空白下一段落：}

### 2.4. 屏幕定位

- 屏幕顶端：H
- 屏幕低端：L
- 屏幕中央：M
- 向下滚动：ctrl+e
- 向上滚动：ctrl+y
- 上一屏：ctrl+b
- 下一屏：ctrl+f
- 上半屏：ctrl+d
- 下半屏：ctrl+u
- 移至屏首：z[return]
- 移至屏中央：z.
- 移至屏尾：z-

### 2.5. 文件定位

- 文件头：gg,
- 文件尾：G

## 3. 增

- 光标处添加：i
- 光标后添加: a
- 行首添加：I
- 行尾添加：A
- 下一行添加：o
- 上一行添加：O

## 4. 撤销&重做

- 撤销：u
- 撤销本行操作：U
- 重做：ctrl+r

## 5. 删

- 删除字符：x
- 删除 n 个字符：nx
- 从左侧删除：X
- 删除主键：d
  - dW: 删到词头
  - dE: 删到词尾
    dB: 删到上一词尾
- s:x+i
- S:dd+i

## 6. 改

- 修改一个单词：r
- 修改主键：c
  - cw：dw+i
- C:d\$+i

## 7. 查

### 7.1 行内查找字母

- 向前查找：F/T
- 向后查找：f/t
- f 到当前，t 到前一个
- 重复查找：; (semicolon)
- 重复向前查找：, (comma)

### 7.2 文件内查找单词

- 向后搜索：/
- 向前搜索：?
- 向后查看结果：n
- 向前查看结果：N
- 向后搜索光标下单词：\*
- 向前搜索光标下单词：\#

## 8. 复制粘贴

- 粘贴光标后：p
- 粘贴光标前：P
- 剪贴/删除：dd
- 复制主键:y
  - yy：复制当前行
  - yw：复制单词
  - y\$:复制至行尾
  - y^：复制至行首
  - ygg:复制至文档首
  - yG：复制至文档尾
  - y20G:从 20 行复制到文档尾
  - Y：复制当前行
