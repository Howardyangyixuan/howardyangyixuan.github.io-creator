---
title: "CSS知识总结3"
date: 2020-05-10T15:16:18+08:00
draft: false
---

# CSS 知识总结 3

啊还有定位，这篇CSS定位和动画一起总结下，然后贴一些做的小练习。

---
<!-- TOC -->

- [CSS 知识总结 3](#css-%e7%9f%a5%e8%af%86%e6%80%bb%e7%bb%93-3)
  - [1. CSS定位](#1-css%e5%ae%9a%e4%bd%8d)
    - [1.1. CSS的层叠顺序（由上到下）：](#11-css%e7%9a%84%e5%b1%82%e5%8f%a0%e9%a1%ba%e5%ba%8f%e7%94%b1%e4%b8%8a%e5%88%b0%e4%b8%8b)
    - [1.2. position定位](#12-position%e5%ae%9a%e4%bd%8d)
  - [2. CSS层叠上下文](#2-css%e5%b1%82%e5%8f%a0%e4%b8%8a%e4%b8%8b%e6%96%87)
    - [2.1. 层叠上下文顺序（由上到下）：](#21-%e5%b1%82%e5%8f%a0%e4%b8%8a%e4%b8%8b%e6%96%87%e9%a1%ba%e5%ba%8f%e7%94%b1%e4%b8%8a%e5%88%b0%e4%b8%8b)
  - [3. CSS动画](#3-css%e5%8a%a8%e7%94%bb)
    - [3.1. CSS渲染：浏览器原理，chrome，微软，firefox](#31-css%e6%b8%b2%e6%9f%93%e6%b5%8f%e8%a7%88%e5%99%a8%e5%8e%9f%e7%90%86chrome%e5%be%ae%e8%bd%affirefox)
    - [3.2. 如何更新样式](#32-%e5%a6%82%e4%bd%95%e6%9b%b4%e6%96%b0%e6%a0%b7%e5%bc%8f)
    - [3.3. CSS动画优化](#33-css%e5%8a%a8%e7%94%bb%e4%bc%98%e5%8c%96)
    - [3.4. CSS动画制作](#34-css%e5%8a%a8%e7%94%bb%e5%88%b6%e4%bd%9c)
      - [3.4.1. transform动画](#341-transform%e5%8a%a8%e7%94%bb)
    - [3.5. transition动画过渡:](#35-transition%e5%8a%a8%e7%94%bb%e8%bf%87%e6%b8%a1)
      - [3.5.1. animation](#351-animation)

<!-- /TOC -->

## 1. CSS定位
### 1.1. CSS的层叠顺序（由上到下）：
内联子元素/文字：后面的文字盖后面>浮动元素：浮动里的文字与浮动同级>块级子元素>border>背景

背景：border外边沿围成的范围

### 1.2. position定位
1. static：默认选项，在文档流中
2. Relative：显示有偏移，实际位置不变
    1. 和top/left配合使用
    2. 和absolute结合使用，做absolute的爸爸
    3. 配合z-index
        1. 默认auto，计算出来是0，但不能写0
        2. 大的盖住小的
        3. Z-index管理
3. Absolute
    1. 根据relative来定位
        1. 相对于祖先元素中，最近的一个定位元素，即不是static都是定位元素。
    2. 脱离原来场景
        1. 对话框关闭按钮，top:0;right:0;
        2. 鼠标提示，white-space：nowrap文字内容不换行
    3. 配合z-index
    4. 经验：
        1. Top/left都要写，否则有些浏览器会错乱
        2. 善用left：100%
        3. 善用left：50%+负margin
4. Fixed
    1. 相对于适口定位：viewport
        1. 适口：能看到的区域
        2. 如果在所属的元素有特殊属性就不是相对适口
            1. transform:
        3. 使用场景
            1. 广告
            2. 回到顶部按钮
        4. 配合z-index
    2. 经验
        1. 手机上不要用
5. Sticky
    1. 2019.9兼容性差
    2. 粘滞定位：快要看不见，就黏在这里，做导航

## 2. CSS层叠上下文

### 2.1. 层叠上下文顺序（由上到下）：
1. Zindex0及以上/除static都是0>内联子元素/文字>浮动元素：文字同级>块级子元素>border>背景>zindex->html元素
2. 作用域
    1. 上述中的一级中，index
3. 创建层叠上下文
    1. html元素
    2. zindex=值，不为auto
    3. position：fixed
    4. opacity<1:影响所有元素
    5. tranform
    6. Flex
4. 负zindex不一定可以逃出div，因为可能有新的层叠上下文

## 3. CSS动画

### 3.1. CSS渲染：浏览器原理，chrome，微软，firefox
1. 根据html构建html树：DOM
2. 根据css构建css树，CSSOM
3. 合并DOM和CSSOM，得到渲染树render tree(用户看到的)
4. Layout布局(文档流，盒模型，计算大小，位置)
5. Paint绘制（颜色、阴影）
6. Composite合成（根据层叠关系）

### 3.2. 如何更新样式
1. Js加样式
    1. div.style.background=‘red’
2. Js直接加类
    1. div.classList.add
3. 更新过程
    1. Js->style->layout>paint->composite
        1. div.remove,其他relayout
    2. Js->style->paint->composite未改layout
        1. 改背景颜色
    3. Js->style->composite：transform
4. 性能
    1. 更新过程决定性能
    2. https://csstriggers.com/
        1. blink 谷歌
        2. gecko ie
        3. webkit safari
        4. edgehtml Edge
    3. 高手不用left，因为性能差
        1. 用transform，不用repaint和payout，性能好

### 3.3. CSS动画优化
1. js优化：requestAnimationFrame代替setTimeout或setInterval
    1. setTimeout(()=>{demo.remove()},1000)
2. css优化：will-change或translate

### 3.4. CSS动画制作

#### 3.4.1. transform动画
1. 位移translate(transform:translate())
    1. Xy：translateX，translateY
    2. Z:需要有视点，perspective
    3. translate(x,y)
    4. translate3d(x,y,z)
    5. 百分号：为自己的宽度的百分比
    6. 用绝对定位居中
        1. top：50%
        2. left：50%
        3. tranform(-50%,-50%)
2. 尺寸scale
    1. transform：scale(2,2);
    2. 用的少，因为容易模糊
3. 旋转rotate
    1. 默认顺时针
    2. transform: rotate(180deg/0.5turn);沿着z轴旋转，即垂直平面
    3. rotateX，rotateY
    4. rotate3d
4. 倾斜skew
    1. Xy轴偏移角度
5. 变化时间transition
    1. Transition:all 1s;
6. 用空格隔开即可，重复写transform会覆盖
    1. none
7. 调试
    1. 点数字+shift+上/下：10个移动
    2. 点数字+option+上/下：0.1移动

### 3.5. transition动画过渡:
1. 填充中间帧，补间动画
2. transition: <property> <duration> <timing-function> <delay>;
3. <timing-function>
    1. 淡入ease-in
    2. 淡出ease-out
    3. step-start
4. <delay>延迟
    1. 秒数
5. 单位
    1. 秒s
    2. 毫秒ms
6. 不能过渡
    1. display:none=>block
    2. 可见到不可见：visibility：visiable=>hiddne;
    3. 颜色可以
    4. 透明度，但不推荐
    5. #xxx=>#xxx.end
7. 两次动画
    1. 先到b
    2. 再到c
    3. setTimeout.add()+div.classList.add

#### 3.5.1. animation
1. @keyframes 名字 + animation: 名字 时间
    1. 0%{transform:none;}
    2. 静帧：24%，73%{}
2. 最后停止
    1. animation: 名字 时间 forwards
3. Animation：时长｜过度方式｜延迟｜次数｜方向｜填充模式｜是否暂停｜动画名；
    1. 方向：alternative交替，前后动画
    2. 填充模式：forwards
    3. 暂停：animationPlayState：running/pause

最后贴上制作的跳动的心❤️，这里是[效果](http://js.jirengu.com/fodop/1/)还有[源码](http://js.jirengu.com/fodop/1/edit)
![CSS跳动的心](/css_hearts.png)
![CSS跳动的心](/css跳动的心.gif)

开心呀hhh礼物也都弄好了，不过感觉怎么也要9月才能送出去吧...今天重量又📉啦哈哈，然后虽然知道其实大概率没结果的，但就是决定要做。

最后提醒自己一下，以后博客还是不能拖，学完赶快写，不能一次写太多！！！