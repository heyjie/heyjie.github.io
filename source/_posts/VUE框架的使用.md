---
title: VUE框架的使用
date: 2021-01-06 09:39:47
categories: 
    - 程序开发
    - 前端
tags:
    - css
    - html
---

<!-- more -->

## vue模板语法

使用差值表达式将数据填充到html,实例参数如下
* el:元素挂载位置（值是css选择器或dom元素）
* data:模型数据（值是对象）

```html
<body>
    <div id="app">
        <div>{{msg}}</div>
        <!--差值表达式支持基本运算-->
        <div>{{msg + '----' + 123}}</div>
    </div>
</body>
<script src="js/vue.js">
<script>
    var vm = new Vue({
        el: '#app',
        data: {
            msg:'hello vue'
        }
    });
</script>
```

### 指令
指令是自定义属性，以v开始，比如：v-cloak。
v-cloak可以隐藏未编译的mustache标签直到实例准备完毕，减少闪动。
```css
[v-cloak] {
    display:none;
}
```
```html
<div v-cloak>
    {{msg}}
</div>
```

使用v-text比v-cloak加差值表达式的方式更简单
```html
<div v-text='msg'>
</div>
```

v-html可以填充html，有安全问题存在，所以只能自己网站数据使用，第三方的数据不要用
```html
<body>
    <div id="app">
        <div v-html="msg"></div>
    </div>
</body>
<script src="js/vue.js">
<script>
    var vm = new Vue({
        el: '#app',
        data: {
            msg:'<h1>HTML</h1>'
        }
    });
</script>
```

v-pre填充原始信息,不编译原样输出
```html
<div v-pre>{{msg}}</div>
```

v-once只编译一次，如果显示的信息不需要修改，可以使用v-once提高性能
```html
<div v-once>{{msg}}</div>
```

v-model是双向数据绑定,修改页面的数据值会发生变化，反过来也一样会变
```html
<div id="app">
    <div>{{msg}}</div>
    <div>
        <input type="text" v-model="msg">
    </div>
</div>
```
### 事件
v-on用来处理事件
```html
<input type='button' v-on:click='num++'>
```

v-on的另一种写法
```html
<input type='button' @click='num++'>
```

使用方法处理事件
```html
<body>
    <div id="app">
        <div>{{num}}</div>
        <button @click='handle'></button>
    </div>
</body>
<script src="js/vue.js">
<script>
    var vm = new Vue({
        el: '#app',
        data: {
            num:'0
        }
    },
    methods:{
        handle: function(){
            // this是vue的实例对象
            this.num++;
        }
    });
</script>
```

事件对象的传递,方法调用的事件对象是最后一个参数，固定名字是`$event`
```html
<button v-on:click='say("hi",$event)'>say hi</button>
```
直接使用方法名称，默认会传递事件对象
```html
<button v-on:click='say'>say hi</button>
```

事件修饰符  
.stop阻止冒泡,冒泡是指子事件会影响父事件的执行,代替原生js中的`event.stopPropagation()`方法
```html
<a v-on:click.stop="handle">跳转</a>
```
.prevent阻止标签默认行为，不让a标签跳转，代替原生js中的`event.preventDefault()`方法
```html
<a v-on:click.prevent="handle">跳转</a>
```

按键修饰符  
.enter回车键触发
```html
<input v-on:keyup.ennter='submit'>
```
.delete删除键触发
```html
<input v-on:keyup.delete='handle'>
```

自定义按键修饰符，如果默认的按键修饰符不够用，可以键码自定义
```
Vue.config.keyCodes.f1=112
```

### 属性绑定
v-bind是绑定属性指令，可以动态处理属性的值
```html
<a v-bind:href='url'>跳转</a>
```
可以简写成冒号的形式
```html
<a :href='url'>跳转</a>
```

v-model实现原理是用来属性绑定和事件绑定组合的
```html
<input v-bind:value="msg" v-on:input="msg=$event.target.value">
```

### 样式绑定
class样式可以用v-bind来绑定
```html
<!--动态控制样式显示，active是类名，控制isActive值显示是true-->
<div v-bind:class="{active:isActive}"></div>
<!--数组控制样式显示，activeClass的值要定义类名，值置空就是取消类名-->
<div v-bind:class="[activeClass,errorClass]"></div>
```

style样式处理
```html
<!--动态控制样式显示，activeColor和fontSize的值要在vue中定义-->
<div v-bind:class="{color:activeColor,fontSize:fontSize}"></div>
<!--数组控制样式显示，baseStyle的值要在vue中定义完整的样式对象-->
<div v-bind:class="[baseStyle,OverridingStyle]"></div>
```

### 分支循环结构

v-if控制元素是否渲染到页面
```html
<div v-if='num>=90'>好</div>
<div v-else-if='num<90&&num>=60'>中</div>
<div v-else>差</div>
```

v-show控制元素是否显示（已经渲染）
```html
<!--用flag的值控制显示，值为true是显示-->
<div v-show='flag'>测试</div>
```

循环结构
```html
<li v-for='item in list'>{{item}}</li>
<li v-for='(item,index) in list'>{{item}}+"---"+{{index}}</li>
```
使用key帮助vue区分不同元素，从而提高性能
```html
<li :key='item.id' v-for='(item,index) in list'>{{item}}+"---"+{{index}}</li>
```

循环和if一起使用
```html
<div v-if='value==12' v-for='(value,key,index) in object'></div>
```

## vue常用特性
### 表单操作
表单值通过v-model双向绑定
```html
<input type='text' v-model='uname'>
```

单选按钮要定义value属性，然后通过v-model修改值，这里的gender值为1时选择第一个
```html
<input type='radio' value='1' v-model='gender'>
<input type='radio' value='2' v-model='gender'>
```

多选单选按钮要定义value属性，然后通过v-model修改值，这里的hobby值为[2,3]时选择第二和第三个
```html
<input type='radio' value='1' v-model='hobby'>
<input type='radio' value='2' v-model='hobby'>
<input type='radio' value='3' v-model='hobby'>
```

表单提交要通过事件处理时，应该用`.preve`阻止默认提交行为
```html
<input type='submit' value='提交' @click.preve='handle'>
```

下拉框如果是单选，occupation要给一个值，比如occupation值为1时选中教师
```html
<span>职业：</span>
<select v-model='occupation'>
    <option value='0'>请选择职业</option>
    <option value='1'>教师</option>
    <option value='2'>软件工程师</option>
    <option value='3'>律师</option>
</select>
```

下拉框如果是多选，occupation要给一个数组，比如occupation值为[2,3]时选中软件工程师和律师
```html
<span>职业：</span>
<select v-model='occupation' multiple="true">
    <option value='0'>请选择职业</option>
    <option value='1'>教师</option>
    <option value='2'>软件工程师</option>
    <option value='3'>律师</option>
</select>
```

### 表单修饰符
表单修饰符number是转换为数值
```html
<input type='number' v-model.number='age'>
```

表单修饰符trim是去掉首尾空格
```html
<input type='number' v-model.number='age'>
```

表单修饰符lazy是将input事件转换为change,input每次输入都会触发，change是失去焦点时触发
```html
<input type='number' v-model.number='age'>
```

### 自定义指令

自定义指令语法
```js
Vue.directive('focus'{
    inserted: function(el){
        //el表示指令绑定的元素
        //该指令获取元素焦点
        el.focus();
    }
})
```

自定义指令用法
```html
<input type="text" v-focus>
```

带参数的自定义指令
```js
Vue.directive('color'{
    inserted: function(el,binding){
        //el表示指令绑定的元素
        //该指令获取元素焦点
        el.style.backgroundColor=binding.value.color;
    }
})
```

带参数的自定义指令用法
```html
<input type="text" v-color='{color:"orange"}'>
```

局部自定义指令,这段代码是在vue实例内部编写
```js
directives:{
    focus:{
        inserted: function(el){
            el.focus()
        }
    }
}
```

### 计算属性

表达式计算逻辑复杂时，使用计算属性可以使模板内容更加简洁
```js
computed: {
    reversedMessage: function(){
        //翻转字符串
        return this.msg.split('').reverse().join('')
    }
}
```

使用方法，计算属性和方法不同，计算属性基于依赖缓存，方法则没有缓存
```html
<div>{{reversedMessage}}</div>
```

### 侦听器

数据变化时执行异步或开销大的操作  
下面代码使用侦听器,当firstName和lastName变化时修改fullName
```js
data:{
    firstName: 'Jim',
    lastName: 'Green',
    fullName: 'Jim Green'
}
watch: {
    firstName: function(val){
        //val表示变化后的值
        this.fullName = val + this.lastName
    },
    lastName: function(val){
        this.fullName = this.firsName + val;
    }
}
```

异步使用示例
```js
data:{
    uname:'',
    tip:''
},
methods:{
    checkName: function(uname){
        var that=this;
        //模拟接口调用
        setTimeout(function(){
            if(uname=='admin'){
                that.tip='用户名已经存在，请更换一个‘;
            }else{
                that.tip='用户名可以使用'
            }
        }，2000)
    }
},
watch: {
    uname: function(val){
        //调用后台接口验证用户名
        this.checkName(val);
        //修改提示
        this.tip='正在验证...';
    }
}
```

修饰符lazy是修改事件为change,change是失去焦点时触发验证
```html
<input type="text" v-model.lazy='uname'>
<span>{{tip}}</span>
```

### 过滤器
全局过滤器语法
```js
Vue.filter('过滤器名称'，function(value){
    //过滤业务逻辑

})
```

过滤器的使用,在竖线后面写过滤器的名称
```html
<div>{{msg | upper}}</div>
<div>{{msg | upper | lower}}</div>
<div v-bind:id="id | formatId"></div>
```

局部过滤器语法
```js
filters:{
    upper: function(){}
}
```

使用带参数的过滤器
```js
Vue.filter('format',function(value,arg1){
    // value就是过滤器传递过来的参数
})
```

带参数的过滤器使用
```html
<div>{{date | format('yyyy-MM-dd')}}</div>
```

### 生命周期

挂载（初始化相关属性）
1. beforeCreate
2. created
3. beforeMount
4. mounted
更新（元素或组件的变更操作）
1. beforeUpdate
2. updated
销毁（销毁相关属性）
1. beforeDestroy
2. destroyed

### 数组操作api

变异方法（修改原有数据）
1. push()
2. pop()
3. shift()
4. unshift()
5. splice()
6. sort()
7. reverse()
替换数组（生成新的数组）
1. filter()
2. concat()
3. slice()

### 修改响应式数据
* Vue.set(vm.items,indexOfItem,newValue)
* vm.$set(vm.items.indexOfItem,newValue)
    1. 参数一表示要处理的数组名称
    2. 参数二表示要处理的数组索引
    3. 参数三表示要处理的数组的值

## vue组件化开发
* 希望尽可能多的重用代码
* 自定义组件的方式不太容易
* 多次使用组件可能导致冲突
通过创建封装好功能的定制元素解决上述问题

### 组件注册
全局组件注册语法
```js
Vue.component(主键名称,{
    data:组件数据,必须是函数,
    template:组件模板内容，必须是单个根元素，可以是模板字符串（ES6语法）
})
```
例如
```js
Vue.component('button-counter',{
    data:function(){
        return{
            count:0
        }
    },
    template:'<button @click="handle">点击了{{count}}次</button>',
    methods:{
        handle:function(){
            this.count++
        }
    }
})
```

### 组件用法
使用是直接用组件名称当标签
```html
<div id="app">
    <button-counter></button-conter>
</div>
```
如果组件命名是驼峰式，就只能在es6字符串中使用，在普通标签页面中要用短横线的方式使用

### 局部组件注册
```js
var ComponentA = { /*...*/ }
var ComponentB = { /*...*/ }
var ComponentC = { /*...*/ }
new Vue({
    el:'#app',
    components: {
        'component-a':ComponentA,
        'component-b':ComponentB,
        'component-c':ComponentC,
    }
})
```
使用示例
```js
var HelloWorld = {
    data:function(){
        return {
            msg: 'HelloWorld'
        }
    },
    template: '<div>{{msg}}</div>'
}

var vm = new Vue({
    el:'#app',
    data:{

    },
    components:{
        'hello-world':HelloWorld
    }
})
```

### vue调试工具
https://github.com/vuejs/vue-devtools

### 组件数据交互
父组件向子主键传值