---
title: 如何使用CSS3加DIV布局
date: 2020-10-31 18:31:59
categories: 
    - 程序开发
    - 前端
tags:
    - css
    - div
---

## css基础知识

引入css的三种方法：
* 行类样式：`<p style="color:sienna;margin-left:20px">`
* 内部样式表：任意位置的`<style>`标签,推荐在头部定义
* 外部样式表：`<link rel="stylesheet" type="text/css" href="mystyle.css">`

<!-- more -->
css特性:
* 层叠性：样式就近原则，那个样式进就应用
* 继承性：子标签会继承父标签的某些样式
* 优先级：继承或* 0,0,0,0; 标签选择器 0,0,0,1; 类 0,0,1,0; ID 0,1,0,0; 行内样式：1,0,0,0; limportant 无穷大

css选择器：
* 标签选择器：p {}
* 类选择器：.class {}
* id选择器： #id {}
* 通配符选择器：* {}

复合选择器：
* 后代选择器：.nav a {}
* 子元素选择器： div>strong {}
* 交集选择器：p.red {}
* 并集选择器：p,span,.red {}
* 链接伪类选择器：a:hover {}表示鼠标经过a标签

标签的显示模式：
* 块级元素：独占一行，高宽边距可控制，宽度默认是父级的100%，是容器里面能放行内和块元素
* 行内元素：一行多个，高宽不能设置，默认宽度就是它本身的宽度，行内元素只能放文本或者其他行内元素
* 行内块元素：一行多个，高宽边距可控制，默认宽度就是他本身内容的宽度
  
标签显示模式切换：
* 块转行：display:inline
* 行转块：display:block;
* 块和行转行内块：display:inline-block;

emmet语法:
* 生成标签直接输入标签名，然后按tab
* 生成多个标签加*，div*3就是三个div标签
* 生成父子关系标签，ul>li
* 生成兄弟关系标签，div+p
* 生成带类名或者带id的标签，.demo或者#two然后按tab键
* 生成自增顺序的类名标签，.demo$*3

## 样式说明

外观：
* color: 颜色
* line-height: 行高
* text-align: center; 文字水平居中对齐
* text-align: right; 文字右对齐
* text-indent: 首行缩进，2em是首行缩进两字符
* text-decoration: 文本修饰，下划线underline,取消下划线none

字体：
* font-size: 字号
* font-family: 字体
* font-weight: 字体粗细，700或bold是加粗，400或normal是不加粗细，数字后面没有单位
* font-style: 字体样式，italic是倾斜，normal是不倾斜
* font:字体连写，字体连写有顺序，字号和字体必须同时出现

背景：
* background-color:背景颜色
* background-image:none|url(url) 背景图片
* background-repeat: repeal 背景平铺
* background-repeat: no-repeal 背景不平铺
* background-repeat: repeal-x 背景横向平铺
* background-repeat: repeal-y 背景纵向平铺
* background-position: right top; 背景位置右上角
* background-position: x坐标 y坐标; 背景位置
* background-position: left bottom; 背景位置左下角
* background-position: center center; 背景位置水平垂直居中
* background-position: center left; 如果是方位词，背景位置值的顺序无关
* background-position: top; 只指定一个值，另一个默认居中
* background-position: center 10px; 水平居中，垂直10
* background-atachment: fixed; 背景固定
* background-atachment: scoll; 背景滚动
* backgroud: 背景颜色 背景图片 背景平铺 背景滚动 背景位置; 背景简写方式
* backgroud: rgba(0,0,0,0.3); 背景透明

列表：
* list-style: none;去掉列表样式
* float:left;浮动在一行

> 子元素不写样式也会继承父元素的一些样式，可以被继承的样式 font,line,text,color。

### 单行文本垂直居中
文字分为顶线中线和基线,上行基线到下行基线的距离就是行号。中文没有基线，就测两行底线之间的距离
![](hanggao.png)
盒子里的行高就是上距离加内容距离加下距离，行高等于盒子的高度就能实现文本垂直居中
![](hanggao2.png)

## 盒子模型

边框样式
* border-width: 5px; 边框宽度
* border-style: dashed; 边框样式
* border-color: pink; 边框颜色
* border: 5px dotted pinkp; 边框简写
* border: none; 边框去掉
* border-top: 宽度 样式 颜色; 上边框
* border-bottom: 宽度 样式 颜色; 下边框
* border-left: 宽度 样式 颜色; 左边框
* border-right: 宽度 样式 颜色; 右边框
* border-collapse:collapse; 合并边框，表格边框会叠加，合并后就是细线表格
* border-radius:50%; 高度的一半,圆角边框

圆角边框
* border-radius: 20px 20px 20px 20px; 圆角边框简写，左上 右上 右下 左下
* border-top-left-radius:20px; 按顺序设置可以分别设置四个角，左上 右上 左下 右下
* border-top-right-radius:20px;
* border-bottom-left-radius:20px;
* border-bottom-right-radius:20px;

内边距
* padding-left: 左内边距
* padding-right: 右内边距
* padding-top: 上内边距
* padding-bottom: 下内边距
* padding: 10px 10px 10px 10px;上右下左的内边距简写
* padding: 10px;上右下左的内边距简写
* padding: 10px 10px;上下和左右内边距简写
* padding: 10px 10px;上和左右和下内边距简写
> 内边距会撑大盒子增加盒子宽度，没有宽度的盒子指定内边距则不会撑开盒子。比如父盒子指定了宽度子盒子不指定宽度设置内边距是和父盒子一样宽的，如果子盒子指定了宽度再给内边距则会撑破父盒子（比父盒子宽）

外边距
* margin: 10px; 
* marrgin-top: 50px;
* margin: 0 auto; 块级盒子居中对齐，注意盒子要设置宽度 

盒子阴影
* box-shadow: 水平阴影 垂直阴影 模糊 影子大小 阴影颜色 内/外阴影;
* box-shadow: 0 15px 30px rgb(0,0,0,.3);

元素默认就有内外边距，清除内外边距：
```
* {
    padding:0;
    margin:0;
}
```
> 行内元素为了兼容性，尽量只设置左右内外边距

### 外边距的合并
两个上下相邻的盒子设置了外边距，则取两个值中的较大者合并，这叫外边距塌陷现象
![](taxian.png)

垂直的外边距合并，两个嵌套关系的块元素，如果父元素没有上内边距及边框。父元素的上外边距会与子元素的上外边距合并，合并为两者中的较大值
![](taxian2.png)

> 盒子有浮动或者绝对定位（固定定位）属性也能解决外边距合并的问题

### 盒子模型的稳定性
根据盒子模型稳定性，建议优先使用宽度，其次内边距，再次外边距
* width > padding > margin

## 布局
### 浮动
css布局的三种机制
1. 普通流（标准流）
   * 块级元素会独占一行，从上往下排列
     * 常用元素：div,hr,p,h1~h6,ul,ol,dl,form,table 
   * 行类元素按照顺序从左到右排列
     * 常用元素：span,a,i,em 
2. 浮动
   * 让盒子从普通流中浮起来，主要用于让多个块级盒子一行显示
3. 定位 
   * 将盒子定在浏览器的某一位置

浮动的作用
1. 让多个盒子水平排列成一行，使浮动成为布局重要手段。
2. 可以实现盒子的左右对齐等
3. 浮动控制图片，实现文字环绕

* float: none/left/right; 不浮动和左浮动右浮动

浮动列表元素
> 实际使用的导航栏中，我们不会用标签a而是用li包含连接(li+a)的做法
1. li+a语义清晰，一看就是有条理的列表型内容
2. 如果直接用a,搜索引擎容易辨别为有堆砌关键字的嫌疑（故意堆砌关键字容易被搜索引擎有降权的风险），从而影响网站排名

### 清除浮动
父级盒子很多情况下，不方便给高度，但是子盒子浮动就不占有位置，最后父盒子高度为0，就影响了下面的标准流盒子
![](/qingchu.png)
![](/qingchu2.png)
清除浮动就是为了解决父级元素因为子级浮动引起的内部高度为0的问题。清除后父级就会根据浮动盒子自动检测高度。父级有了高度就不会影响下面的标准流了
1. 额外标签法清除浮动，在最后一个浮动标签后面添加一个不浮动的div标签,样式写clear:both
2. 浮动标签的父级样式添加:overflow:hidden|auto|scroll 都可以实现清除浮动，该样式是溢出样式隐藏
3. 使用after伪元素清除浮动，给父级添加以下样式就能清除浮动
![](/qingchu3.png)
3. 使用双伪元素清除浮动，给父级添加以下样式就能清除浮动
![](/qingchu4.png)

什么时候清除浮动
* 父级没高度
* 子盒子浮动了
* 影响下面布局了
  

### CSS属性书写顺序
1. 布局定位属性： display/position/float/clear/visibility/overflow (display第一个写，关系到模式)
2. 自身属性：width/height/margin/padding/border/background
3. 文本属性：color/font/text-decoration/text-align/vertical-align/white-space/break-word
4. 其他属性（CSS3）：content/cursor/border-radius/box-shadow/text-shadow/background:linear-gradiennt ...
   
### 布局流程
1. 必须确定页面的版心（可视区），我们测量可知。
2. 分析页面中的行模块，以及每个行模块中的列模块。其实页面布局，就是一行一行罗列而成
3. 制作HTML结构，要遵循现有结构后有样式的原则。结构永远最重要。
4. 开始运用盒子模型的原理，通过DIV+CSS布局来控制网页的各个模块。

## 定位

### 边偏移
定位的盒子要通过边偏移来移动位置，定位的盒子有边偏移才有价值，一般有定位的地方就有边偏移

* top:80px; 顶端偏移量，定义元素相对于其父元素上边线的距离
* bottom:80px; 底部偏移量，定义元素相对于其父元素下边线的距离
* left:80px; 左侧偏移量，定义元素相对于其父元素左边线的距离
* right:80px; 右侧偏移量，定义元素相对于其父元素右边线的距离

### 定位模式
在不同情况下要用到不同的定位模式
* position: static; 静态定位,默认模式相当于none,不要定位的时候用
* position: relative; 相对定位，相对于原来在标准流中的位置来移动，在原有标准流的区域继续占有（保留位置）
* position: absolute; 绝对定位，绝对定位以父级位置来移动，父级没有定位就以浏览器为准（不保留位置）
* position: fixed; 固定定位，以浏览器可视窗口为准固定位置，不随滚动条滚动而滚动，不保留原位置

> 父级要占有位置，子级要任意摆放，所以经常要使父级相对定位，子级绝对定位（子绝父相）

![](dingwei.png)


绝对定位居中对齐，先让盒子左侧移动到父级的水平中心位置，再让盒子向左移动自身宽度的一半
![](dingwei2.png)

> 固定定位居中对齐，`width:100%`加`text-align:center`是通栏居中对齐

堆叠顺序`z-index`，在使用定位时可能会出现盒子重叠，加了定位的盒子后面的盒子会压住前面的盒子，可以用`z-index`层叠等级改变盒子的堆叠顺序
![](dingwei3.png)
* z-index 取值可以是正负数或0，数值越大，盒子越靠上
* 如果取值相同，则按书写顺序，后来居上
* 取值后面没有单位

定位会改变display属性，display是显示模式，可以改变显示模式的方法有
* 可以用inline-block转换为行内块
* 可以用浮动float默认转换行内块（类似行内块，并不完全一样，因为浮动是脱标的）
* 绝对定位和固定定位也和浮动类似，默认转换为行内块

## css高级技巧
### 元素的显示与隐藏
display设置或检索对象如何显示，不保留位置
display:nono; 隐藏对象
display:block; 除了转换为块元素，同时还有显示元素的意思

visibility设置对象隐藏,保留位置
visibility:hidden; 隐藏
visibility:visible; 显示

overflow检索或设置对象超出其指定高宽时如何管理内容
overflow:visible; 不隐藏内容也不显示滚动条
overflow:hidden; 超出部分隐藏
overflow:scroll; 不管内容是否超出，总是显示滚动条
overflow:auto; 超出自动显示滚动条

> overflow可以清除浮动，可以保证盒子的内容不会超出盒子

### css用户界面样式
鼠标样式cursor,属性：default 默认、pointer 小手、move 移动、text 文本、not-allowed 禁止

轮廓线outline
用于绘制元素周围的一条线，在边框的外围，可起到突出元素的作用。
```
outline: outline-color | outline-style | outline-width;
```
常用的方式是：outline:0; 或者 outline:none

防止拖拽文本域，让textarea标签右下角不可拖拽
resize:none;

### 垂直居中
vertical-align垂直对齐，只针对于行内元素和行内块元素

![](chuizhi.png)
基于文本的四条线设置垂直对齐

```
vertical-align:baseline | top | middle | bottom
```
> 主要用于控制文字和图片和表单之间垂直对齐关系

![](chuizhi2.png)

> 文字和图片默认是基线对齐，所以图片底侧会产生空白,改成其他对齐方式就能去除图片底侧空白，或者把图片转换成块元素也能去除

![](chuizhi3.png)

### 文本溢出省略
```
/*先强制一行内显示文本*/
white-space: nowrap;
/*超出部分隐藏*/
overflow: hidden;
/*用省略号替代超出部分*/
text-overflow: ellipsis;
```
![](shenglue.png)

### 精灵技术

精灵是将网页中的多个背景图像整合到一张图中，减少服务器请求图片次数。网页中只需要精灵图中的某个小图。

![精灵图](jingling.png)

> 精确测量图标在精灵图的位置和大小，先设置盒子宽高，然后利用背景位置（background-position）确定精灵图位置

滑动门技术，可以让特殊形状的背景图自适应元素内容，使背景图可以自由拉伸滑动。
> 利用精灵图设置文本的左侧背景和右侧背景，设置padding拉伸，实现滑动门

### 负值
![](fuzhi.png)
利用margin负值让盒子边框相交的部分变细

```
float: left;
margin-left: -1px
```
> 如果要鼠标经历过突出边框，就要用到定位

### 三角形
![](sanjiaoxing.png)

使用css边框模拟三角形，宽度高度要为0。四个边框都写，保留需要的边框颜色，其他位置边框颜色是透明（rransparent）。为了兼容性要设置font-size:0;line-height:0;