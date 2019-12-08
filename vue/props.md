# Props原理
<font color=gray size=2>2019-12-05 09:47:37 +0800</font>
<font color=gray size=2>categories: dotnet</font>
---



---

示例：
```javascript
<div id="app">
    <pCom :txt="parentText"></pCom>
</div>

var app=new Vue({
    el:"#app",
    data:{
        parentText:"I am is parent"
    },
    components:{
        pCom:{
            props:{
                txt:""
            },
            template:"<p>父组件传入的值{{txt}}</p>",
        }
    }
})
```

#### 问题
+ 父组件怎么传值给子组件的props
+ 子组件如何读取props
+ 父组件data更新，子组件的props如何更新
+ 为何子组件的props更新，不会影响父组件，即单向数据流是如何实现的。

#### 关键知识点
+ with

---

#### 解答



1.首先我们来解释父组件怎么传值给子组件的props，这就用到了with的机制
+ 首先分析一下示例传值：
    * 示例中设置了一个子组件，子组件在props初始化了txt
    * 在使用pCom组件的时候，使props中的txt用v-bind绑定到了父组件的parentText，这也部分解释了，为何子组件的值发生改变，并不会影响父组件，因为父组件的data是主题，子组件的props是观察者（观察者模式的两方）
+ 提出子问题
    * 子组件在使用的时候为何能用父组件的值
    这个问题看起来很好解答，因为子组件在父组件的components定义了，那为何定义了就可以用父组件的data值，这也衍生了第二个问题
    * 子组件既然可以用父组件的值，为何不直接使用，为何要出现props
这一切问题都是因为采用with机制
```javascript
(function(){
    with(this){
        return _c("div",{staticId;"app"},[
            _c("pCom",{attrs:{"txt":parentText}})//渲染pCom这个组件
        ],1)
    }
})
``` 
父组件的模板components都会被渲染成以上的模板渲染函数，因此子组件props中的属性都实际上在attrs中等于this.parentText(使用with)，this指向父组件。
但是这个attrs中属性之后怎么更改都不会影响父组件，当然这也解释了**为何父组件传过来是一个对象时，子组件对对象进行改变，父组件中的对象也发生了改变，这就是因为传过来的是对象的引用**。
**但是：**
&emsp;&emsp;以上只解释了怎么传值，那父组件改变时，子组件怎么也知道改变的，with可没有这个功能，这个就牵扯到响应式原理，请看[响应式原理](./响应式原理.md)这篇文章，子组件会将props中每个属性设置为响应式，设置get，set函数。父组件中相应属性也会收集该依赖。
这也解释了第三个和第四个问题。

2.子组件如何读取props
&emsp;
子组件props中每个属性都有get和set函数，读取和设置会触发函数。



---

**参考资料**