# watch原理
<font color=gray size=2>2019-12-12 09:58:41 +0800</font>
<font color=gray size=2>categories: dotnet</font>
---

#### 要解决的问题
1、监听的数据改变时，watch 如何工作

2、设置 immediate 时，watch 如何工作

3、设置了 deep 时，watch 如何工作

---

#### 概述
计算属性watch是用来观察和响应vue实例上数据的变动，他可以设置赋值一个方法，监听对应属性的改变，并做一些额外操作，通常用来处理属性依赖，如下：
```javascript
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```
但显然上面实例用computed更好，无需每一个属性都设置一个watch
但watch并非没有用，当你是要针对一个属性做一些操作，比如某个属性改变时，就做ajax操作，这个时候显然watch更适合你的要求

**监听的数据改变时，watch是如何工作的**
watch 在一开始初始化的时候，会 读取 一遍 监听的数据的值，于是，此时 那个数据就收集到watch的watcher了。然后你给 watch 设置的 handler ，watch 会放入 watcher 的更新函数中。当 数据改变时，通知 watch 的 watcher 进行更新，于是 你设置的 handler 就被调用了。

**设置immediate时，watch会怎么工作**
当你设置了immediate时，是不需要在数据改变时才会触发，而是在初始化watch时，再读取了监听的数据值之后，立刻就会调用一遍你设置的监听回调，然后传入刚读取的值

**设置deep时，watch怎么工作**
watch的deep属性时很重要的一个属性，针对于对象属性，如果我们没有设置deep属性，当对象内部改变时，时不会调用handler，但是设置了deep，就会监听到。 这也是和Object.defineProperty-set最大的不同

---

**参考资料**
&emsp;&emsp;[Watch - 白话版](https://zhuanlan.zhihu.com/p/53220088)