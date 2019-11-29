```Javascript
function Timer() {
  this.s1 = 0;
  this.s2 = 0;
  setInterval(() => this.s1++, 1000);
  setInterval(function () {
    this.s2++;
  }, 1000);
}

var timer = new Timer();

setTimeout(() => console.log('s1: ', timer.s1), 3100);
setTimeout(() => console.log('s2: ', timer.s2), 3100);
// s1: 3
// s2: 0

```

**解析**
1.我相信很多人粗略一看，会以为这道题是异步导致的，而忽略了Timer中两个函数的区别。
2.仔细一看，发现Timer中两个匿名函数，一个是箭头函数，一个是普通函数，这时候你想到了什么？
&emsp;&emsp;<font color=red size=13>this的指向</font>
3.箭头函数中，this的作用域与父函数一致，所以<font color=red>setInterval(() => this.s1++, 1000);</font>中的<font color=red>this.s1</font>指向了<font color=red>Timer中的this.s1;</font>3100ms后执行了三次，所以为3，这个没有多大疑问，关键是下面的<font color=red>this.s2</font>;
4.在js中，this的指向并不是函数声明时确定的，而是函数执行的时候确定的，谁调用了这个方法，这个方法的this就指向谁，但是<font color=red>   setInterval(function () {this.s2++;}, 1000);</font>为匿名函数，并没人调用它，根据设定，没人调只能委托给window，所以匿名函数的this都指向window，所以上述语句等价于<font color=red>setInterval(function () {window.s2++;}, 1000);</font>
5.所以并未对timier的s2进行任何操作，所以输出时为s2:0
6.如果这时对window.s2进行输出，会得到NaN

**如果上面的题做错了，请少年别走！！你还有拯救的机会**

## this
>>this是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里调用。

万望少年好好思考这句话的含义！！！
#### this的四种绑定规则
this机制与动态作用域很相似，是在运行时在被调用的地方动态绑定的。
this指向的绑定规则有四种：
+ 默认绑定
+ 隐式绑定
+ 显式绑定
+ new绑定

##### 默认绑定
这是最直接的一种方式，但是是优先级最低的绑定方式，只有在其他绑定方式不成立的情况下，才会使用这种绑定方式（绑定中默认值）
```javascript
function test(){
  console.log(this.a);
}

var a=2;//变量声明到全局对象
test();
```
这个可能大家会和以前的我有疑问，但是记住this在默认绑定中绑定的永远是全局对象，即使你是在外层包了一个函数，另外，如果将var a=2;变为let a=2;则输出为undefined。具体原因请看另外一篇博客：
&emsp;[let 与 var](./let_var.md)
#### 隐式绑定
这种情况发生在调用位置存在上下文对象的情况，它的优先级大于默认绑定
这种绑定就回答了上面的问题，因为无论怎么包函数，上下文对象都是window，除非你再声明一个上下文对象，并将函数绑定在该上下文对象上，才行！
```javascript
function foo() {
  console.log(this.a);
}

let obj1 = {
  a: 1,
  foo,
};

let obj2 = {
  a: 2,
  foo,
}

obj1.foo();   // 输出 1
obj2.foo();   // 输出 2
```
#### 显式绑定
这种方法是使用Function.prototype中的call(),apply()和bind()，它的优先级大于前面两种。
这三个函数都可以改变函数的this指向特定对象：
它们之间的不同之处是：
+ apply()与call()是立即执行函数，而bind()是创建一个新的包装函数，并返回
+ call()与apply()的参数不同，前者接受 this和...args ，后者接受this和[...args],后者适用于函数嵌套的情况
```javascript
function foo() {
  console.log(this.a);  // 输出 1
  bar.apply({a: 2}, arguments);
}

function bar(b) {
  console.log(this.a + b);  // 输出 5
}

var a = 1;
foo(3);
```
#### new绑定
最后一种，是new操作符产生this的绑定,它的优先级最高。这种比较好理解，先讲一下new的机制：
+ 创建一个全新的对象。
+ 这个新对象会被执行 [[Prototype]] 连接。
+ 这个新对象会绑定到函数调用的 this。
+ 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。
```javascript
function New(Constructor, ...args){
    let obj = {};   // 创建一个新对象
    Object.setPrototypeOf(obj, Constructor.prototype);  // 连接新对象与函数的原型
    return Constructor.apply(obj, args) || obj;   // 执行函数，改变 this 指向新的对象
}

function Foo(a){
    this.a = a;
}

New(Foo, 1);  // Foo { a: 1 }
```

**那么this到底是什么**
>>this并不是编写的时候绑定的，而是运行时绑定的。它的上下文取决于函数调用时的各种条件，这也就是前面默认绑定的解释。
this的绑定和函数声明的位置没有关系，只取决于函数调用的方式，当一个函数被调用时，会创建一个执行上下文，这个上下文会包含函数在哪里滴啊用、函数的调用方式、传入的参数等信息。this就是这个记录的一个属性，会在函数执行的过程中用到。

***参考***
[加深对 JavaScript This 的理解](https://juejin.im/post/596a28f6f265da6c360a2716)