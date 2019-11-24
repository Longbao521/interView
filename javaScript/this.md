```
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

[参考链接 https://segmentfault.com/q/1010000019188098](https://segmentfault.com/q/1010000019188098)