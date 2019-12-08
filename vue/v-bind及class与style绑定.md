#v-bind及class与style绑定
<font color=gray size=2>2019-11-30 17:02:44 +0800</font>
<font color=gray size=2>categories: dotnet</font>
---

DOM元素经常通过动态绑定class与style样式来实现一些动态效果，本博客主要介绍怎么用v-bind实现这个效果

---

#### 绑定class

+ **对象语法**
给v-bind:class设置一个对象，通过对对象的修改实现class的动态绑定
```javascript
<div id="app">
    <div :class="isActive:true,isError:false"></div>
</div>
....
var app=new Vue({
    el："#app",
    data:{

    }
})
```
以上例子相当于子div类只用isActive，当然后面的boolean可以使用data中的变量动态绑定
:class指定字符串变量也可以使用data和computed中的变量，当该表达式过长或逻辑复杂时，尽量使用computed属性
+ **数组语法**
```javascript
<div id="app">
    <div :class="[activeCls,errorCls]"></div>
</div>
....
var app=new Vue({
    el："#app",
    data:{
        activeCls:"isActivate",
        errorCls:"isError"
    }
})
```
方括号[]里面的内容还可以替换成三元表达式

+ 在组件中使用
```javascript
Vue.component("my-component",{
    template:"<p class='article'></p>"
});
```
---

#### 绑定内联属性
这个方法与绑定class类似，也有对象语法和数组语法，最常用的可以使用以下例子
```javascript
<div id="app">
    <div :style="styles"></div>
</div>
....
var app=new Vue({
    el："#app",
    data:{
        styles:{
            color:"red"
        }
    }
})
```

**参考资料**