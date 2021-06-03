---
title: HTML5和CSS3有哪些新增内容
date: 2020-11-19 17:22:35
categories: 
    - 程序开发
    - HTML
tags:
    - css
    - html
---
html5是超文本标记语言（HTML）的第五次重大修改，有全新的元素，属性和行为


## H5新增内容
### 语义化标签
* \<header> :头部标签
* \<nav> :导航标签
* \<article> :内容标签
* \<section> :块级标签
* \<aside> :侧边栏标签
* \<footer> :尾部标签
![](yuyi.png)
* 语义化标签主要针对搜索引擎
* 可以在页面中使用多次
* 在ie9中，需要把这些元素转换为块级元素
* 移动端更喜欢用这些标签

### 多媒体标签
* \<audio> :音频
* \<video> :视频

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

> 同时添加多个转换，中间用空格隔开，先后顺序会影响转换的结果。有位移和其他属性时，要把位移放在最前面

#### 动画
使用keyframes定义动画
```
@keyframes 动画名称 {
    0%{
        width:100px;
    }
    100%{
        width:200px;
    }
}
```

元素使用动画
```
div {
    width: 200px;
    height: 200px;
    background-color: aqua;
    margin: 100px auto;
    animation-name: 动画名称；
    animation-duration: 持续时间；
}
```

* @keyframes :规定动画
* animation :所有动画属性的简写
* animation-name :规定@keyframes的动画名称（必要）
* animation-duration :规定动画完成的周期，默认是0（必要的）
* animation-timing-function :规定动画速度曲线，默认是"ease"
* animation-delay :规定动画何时开始，默认是0
* animation-iteation-count :规定动画播放的次数，默认是1，还有infinite
* animation-direcition :规定动画是否在下一周期逆向播放，默认是"normal"
* animation-play-state :规定动画是否正在运行或暂停。默认是"running"还有"paused"
* animation-fill-mode :规定动画结束后状态，保持forwards回到起始backwards

动画简写属性：
animation :动画名称 持续时间 运动曲线 何时播放 播放次数 是否反向 动画起始或结束状态
```
animation: myfirst 5s linear 2s infinite alternate;
```

### 3D转换
3d移动
* transform:translateX(100px);
* transform:translateY(100px);
* transform:translateZ(100px);在z轴移动，单位px
* transform:translate3d(x,y,z);其中x,y,z分别表示要移动的方向的距离

透视
perspective:100px;给要透视元素的父盒子添加透视效果,有透视才能看到3d效果

旋转
* transform:rotateX(45deg);
* transform:rotateY(45deg);
* transform:rotateZ(45deg);沿着z正向旋转45度
* transform:rotate3d(x,y,z,deg);沿着自定义轴旋转，deg为角度

3d呈现
* transform-style:flaat;默认值，子元素不开启3d立体空间
* transform-style:preserve-3d; 子元素开启立体空间
> 控制字元素是否开启3d环境，属性指定给父元素但影响的是子元素

### 浏览器的私有前缀
浏览器私有前缀是兼容老版本写法，新版本浏览器无需添加
* -moz-: 代表firefox浏览器私有属性
* -ms-: 代表ie浏览器私有属性
* -webkit-:代表safari、chrome私有属性
* -o-:代表Opera私有属性

使用方法:
-o-border-radius:10px;