---
title: HTML5加CSS3的使用
date: 2020-11-19 17:22:35
categories: 
    - 程序开发
    - 前端
tags:
    - css
    - html
---
html5是超文本标记语言（HTML）的第五次重大修改，有全新的元素，属性和行为

<!-- more -->

## H5新增内容
### 语义化标签
* <header> :头部标签
* <nav> :导航标签
* <article> :内容标签
* <section> :块级标签
* <aside> :侧边栏标签
* <footer> :尾部标签
![](HTML5加CSS3的使用/yuyi.png)
* 语义化标签主要针对搜索引擎
* 可以在页面中使用多次
* 在ie9中，需要把这些元素转换为块级元素
* 移动端更喜欢用这些标签

### 多媒体标签
* <audio> :音频
* <video> :视频

### 表单属性
* type="email" :限制用户输入为email类型
* type="url" :限制用户输入为url类型
* type="date" :限制用户输入为日期类型
* type="time" :限制用户输入为时间类型
* type="month" :限制用户输入为月类型
* type="week" :限制用户输入为周类型
* type="number" :限制用户输入为数字类型
* type="tel" :手机号码
* type="search" :搜索框
* type="color: :颜色选择表单

## CSS3新增内容

### 属性选择器
* E[att] :选择具有att属性的E元素
* E[att="val"] :选择具有att属性且值等于val的E元素
* E[att^="val"] :选择具有att属性且值以val开头的E元素
* E[att$="val"] :选择具有att属性且值以val结尾的E元素
* E[att*="val"] :选择具有att属性且值中含有val的E元素

> 属性选择器的权重是10

### 结构伪类选择器
* E:first-child :匹配父元素中的第一个子元素E
* E:last-child :匹配父元素中的最后一个子元素E
* E:nth-child(n) :匹配父元素中的第n个子元素E
* E:first-of-type :指定类型E的第一个
* E:last-of-type :指定类型E的最后一个
* E:nth-of-type(n) :指定类型E的第n个

> n可以是even(偶数)和odd(奇数),或者是公式2n(偶数)、2n+1(奇数)等

### 伪元素选择器
* ::before :在元素内部的前面插入内容
* ::after :在元素内部的后面插入内容

> 必须有content属性，属于行内元素，权重为1

### 2d转换
* transition: all 0.3s; 动画过渡
* transform: translate(x,y);  #移动到新位置，不影响其他元素位置
* transform: translate(50%,50%);  #移动到新位置，自身宽度的一半
* transform: rotate(45deg);  #顺时针旋转45度，角度为负数时是逆时针旋转
* transform-origin: left bottom;  #转换中心点，可以用方位词像素和百分比
* transform: scale(x,y); #缩放，使用没有单位的数字参数作为缩放倍数