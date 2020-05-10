---
title: "HTML入门笔记2 HTML常用标签"
date: 2020-05-06T11:42:13+08:00
draft: false
---

# HTML 入门笔记 2 - HTML 常用标签

## 1.如何预览 HTML

### 永远不要双击打开 html！要以**普通用户身份**打开，即输入网址

下面列举两种构建本地服务器的例子

### 1.1. 使用 Http-server

1. 命令
   1. http-server . -c-1
   2. -c：缓存，-1 为值
   3. http-server 可缩写 hs
2. 打开后
   1. 点击链接地址后+文件名
   2. 同一个局域网下设备均可访问，可用手机查看网页响应式情况

### 1.2. 使用 Parcel

1.  parcel 文件名

## 2. 常用标签：a 标签

#### 2.1. 作用：

1.  跳转外部页面
2.  跳转内部锚点
3.  跳转邮件&电话

#### 2.2. 属性：

1. Href：超链接
   1. 取值：
      1. 网址
         1. http/https://网址
         2. 无协议网址：//网址
            1. 继承当前
            2. 自动选择???：学了 http 协议就知道了
      2. 路径
         1. http 根目录取决于在哪儿开服务
         2. 绝对路径：/a/b/c
         3. 相对路径，文件的当前目录：a/b/c
         4. 同一目录下：
            1. 直接写文件名
            2. 或 ./
      3. 伪协议
         1. 执行 js，很早前用
            1. javascript:代码;
            2. 空的伪协议：a 标签，又什么都不做
               1. javascript:;
               2. “”空着：会刷新
               3. 写#：会滚到顶部，地址会加#
         2. Id: 写 href=#id：会跳转到 id，会加#id
         3. mailto:邮箱
         4. tel:电话号
2. Target：在哪儿里打开超链接
   1. 内置
      1. \_blank：新标签页
      2. \_top：最顶层
         1. Google 不允许用 iframe 指向它：因为会占便宜
         2. 百度就可以
         3. 并且可以前进后退
      3. \_parent:上一层
      4. \_self：自己
   2. 自命名：在 xxx 中打开，否则创建 xxx
      1. 用 iframe 创建可选的搜索引擎 google/百度
3. download
   1. 下载网页
   2. 很多浏览器不支持
4. rel=no-opener
   1. 防止一个 bug：js 相关

## 3.常用标签：iframe 标签

内嵌窗口，很少使用了，老系统在用，现在用 ajax

## 4.常用标签: table 标签

1. Table 里正确的只有三个，其他的都是错的
   1. thead
   2. tbody
      1. tr：table row
         1. th：table head
         2. td：table data
   3. tfoot
   4. 三个写的顺序与显示无关
      1. 都是头、身体、脚
2. 不写的话，不是对了，而是浏览器帮忙填充了
   1. 默认在 tbody
   2. 只写 td 填充 tr
3. 样式
   1. table-layout
      1. Auto 根据内容调整
      2. Fix 等宽
   2. Border-collapse:collapse
   3. Border-spacing:0px

## 5.常用标签: img 标签

img 标签是可替换元素，面试会考

1. 作用发出 get 请求，展示图片
2. Src 图片地址
   1. 可以是网络地址
3. 属性
   1. Alt：显示失败时显示
   2. height，width
      1. 设置一个，另一个自适应
      2. 底线！不能让图片变形
   3. src
4. 事件
   1. onload
   2. onerror
   3. 调试用 id 找，平时不要用
      1. xxx.onload
5. 响应式：
   1. 自适应宽度：max-width:100%
      1. 不同设备上好的体验

## 6.常用标签: Form 标签

1. 作用
   1. 发 get/post，刷新页面
   2. 表单
2. 属性
   1. Action:请求的地址
   2. method：
      1. get/post
      2. Dialog 兼容性有问题&没人用
   3. Autocomplete 自动建议
      1. username
   4. Target 指定打开页面
      1. 与 a 标签一致
3. 事件
   1. onsubmit
4. 里面的标签
   1. Input：里面不能有新标签
      1. 作用：让用户输入内容
      2. 属性 Type：浏览器会自动适应语言
         1. submit：显示“提交”，不写 type 默认 submitt
         2. text
         3. color
         4. password：不显示出来
         5. Radio：单选，同一个 name
         6. checkbox 多选，一组同一个 name
         7. file：文件
            1. 多选：multiple
         8. hidden
            1. 给机器输入的，给 js 填字符串或 id 1.
            2. name
   2. Button：里面可以任意标签
   3. textarea：多行输入
      1. sytle：resize：none 不可改变大小
   4. select
      1. option
         1. value 真正的值
         2. 里面写的是给用户看的
   5. 事件
      1. onchange
      2. onfocus 聚焦时出发
      3. onblur 离开
   6. 验证器
      1. HTML5 新增，自带
      2. eg.`<input type="text" required>`
5. 注意事项
   1. 一般不监听 input click 事件
   2. form 里的 input 要有 name
   3. form 里要放 type=submit 的事件，否则无法触发
6. 兼容性需要查看文档，js 会讲

   1. video
   2. audio
   3. canvas
   4. svg

### 最后提醒自己一下，Vscode 目录中，同级并排，要看清

最近写完毕设论文要开始做实验了，一个多月改一篇论文，加上中间毕业的事儿，又将是高压了，运动+lc 持续了 2 个月，体重回归，今天 lc 应该到 100 道了算是上道了，然后职业规划调研的也有点眉目了。后续要做的，就是好好想想该怎么读这个研了吧。

感觉做了还不少事儿，最近给她买的东西也陆续到了，心情一直很反复，有的时候觉得很虚无，有时候很有信心。

努力未必。但不努力，不去改变，肯定不行。这是我唯一确定的了。稍微一软弱就想起她说我的那次哈哈。如果有以后，我要努力不会再被说了。
