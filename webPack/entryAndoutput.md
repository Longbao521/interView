#入口(entry)与输出
<font color=gray size=2>2019-12-01 13:53:18 +0800</font>
<font color=gray size=2>categories: dotnet</font>
---

这篇文章主要介绍webpack中的entry属性如何配置

---

#### 入口
在webpack中入口语法可以有以下三种
+ 单个入口语法
```javascript
module.exports = {
    entry="./path/file.js"
}
```
+ 对象语法
```javascript
module.exports = {
    entry:{
        app:"./path/app.js",
        adminApp:"./path/adminApp.js"
    }
}
```
+


---

**参考资料**