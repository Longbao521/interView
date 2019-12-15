# Cookie与Session
<font color=gray size=2>2019-12-09 09:30:47 +0800</font>
<font color=gray size=2>categories: dotnet</font>
---

Cookie可能很多人知道，Session可能就没多少人了解了，而他们内部的机理更没多少人了解了，这篇文章就让我们一起学习以下Cookie与Session到底是什么，他们是怎么工作的，是相伴相生还是相互替代。

---

#### Cookie

你可能经常碰到这些情况，在登录一些网址(如163邮箱)，网页会弹出是否保存密码，点击是之后，即使你关掉网页，关掉电脑，下次打开的时候，也会自动帮你登录，这里就用到了Cookie，另外你的一些浏览痕迹，如你在淘宝逛了哪些商品，都会被偷偷记录下来。这样说，Cookie仅仅只是在浏览器端保存一些浏览痕迹和私密信息吗？
不仅仅这样！
因为HTTP是无状态的协议，所以当浏览器和服务端进行协作时，就会把Cookie保存到报文表中发送给服务端，从而服务端可以识别出特定用户，这有什么好处呢，比如你登录了一个网址，但是你进行一些操作，要发HTTP给服务端请求，但是服务端这些操作是要针对特定用户的，如果你每次都验证一下，岂不是很麻烦，所以就出现了Cookie，它会将你的登录信息保存到本地机器上的小段文本，并在每次请求中都束缚上这些Cookie。
那Cookie的生命周期是多长呢？它会一直存在吗？
显然不是！！！Cookie的内容包括：名字、值、过期时间、路径和域，路径和域一起构成了Cookie的作用范围，当发送请求时，会判断当前Cookie的作用范围是否大于或等于请求资源所在的位置，若是，则将Cookie绑定在请求资源的HTTP请求头上发送给服务器。而若不设置过期时间，那Cookie的生命期为浏览器会话期间，当关闭浏览器，cookie会自动清除，此时Cookie一般保存在内存中，但是如果设置了过期时间，浏览器会将Cookie保存到硬盘中，只有过了过期时间，才会删除，这时，即使关闭网页，再重新打开，也可以使用保存的Cookie。
##### 添加Cookie
```javascript
Cookie cookie = new Cookie("user", "suntao");
cookie.setMaxAge(7*24*60*60);     // 一星期有效
response.addCookie(cookie);
```

##### 获取Cookie
```javascript
// 因为取得的是整个网页作用域的Cookie的值，所以得到的是个数组
Cookie[] cookies = request.getCookies();

for(int i = 0 ; i < cookies.length ; i++){
   String name = cookies[i].getName() ;
   String value = cookies[i].getValue() ;
}    
```
#### Session
说完了Cookie，Cookie是浏览器端保存信息的手段，那Session就是服务端保存信息的手段，当你在淘宝购物的时候，添加购物车，每添加一个产品，都会发送一个HTTP请求，那服务器怎么识别一直是你这个客户呢，有人说Cookie呀，对，但不完全对。
确实用了Cookie，但是服务器怎么识别Cookie，与第一次的Cookie进行匹对吗，那如果浏览器端禁止Cookie呢？服务器有自身保存状态的方法，就是用Session，第一次创建Session，会通过HTTP协议通知客户端，需要在Cookie中加一个Session ID，以后每次发送Cookie时，服务器端只要识别Cookie中的SessionID就可以了，无需每次都将Cookie中所有内容进行比对。如果禁止Cookie，就采用一种URL重写的方法，将sid=***加到get的URL中。
同时，由于Session采用散列表的方式存储，通过Session ID能够很快找到相对应Session。

#### 两者的不同
>1、存取方式的不同
Cookie中只能保管ASCII字符串，假如需求存取Unicode字符或者二进制数据，需求先进行编码。Cookie中也不能直接存取Java对象。若要存储略微复杂的信息，运用Cookie是比拟艰难的。
而Session中能够存取任何类型的数据，包括而不限于String、Integer、List、Map等。Session中也能够直接保管Java Bean乃至任何Java类，对象等，运用起来十分便当。能够把Session看做是一个Java容器类。
2、隐私策略的不同
Cookie存储在客户端阅读器中，对客户端是可见的，客户端的一些程序可能会窥探、复制以至修正Cookie中的内容。而Session存储在服务器上，对客户端是透明的，不存在敏感信息泄露的风险。
假如选用Cookie，比较好的方法是，敏感的信息如账号密码等尽量不要写到Cookie中。最好是像Google、Baidu那样将Cookie信息加密，提交到服务器后再进行解密，保证Cookie中的信息只要本人能读得懂。而假如选择Session就省事多了，反正是放在服务器上，Session里任何隐私都能够有效的保护。
3、有效期上的不同
使用过Google的人都晓得，假如登录过Google，则Google的登录信息长期有效。用户不用每次访问都重新登录，Google会持久地记载该用户的登录信息。要到达这种效果，运用Cookie会是比较好的选择。只需要设置Cookie的过期时间属性为一个很大很大的数字。
由于Session依赖于名为JSESSIONID的Cookie，而Cookie JSESSIONID的过期时间默许为–1，只需关闭了阅读器该Session就会失效，因而Session不能完成信息永世有效的效果。运用URL地址重写也不能完成。而且假如设置Session的超时时间过长，服务器累计的Session就会越多，越容易招致内存溢出。
4、服务器压力的不同
Session是保管在服务器端的，每个用户都会产生一个Session。假如并发访问的用户十分多，会产生十分多的Session，耗费大量的内存。因而像Google、Baidu、Sina这样并发访问量极高的网站，是不太可能运用Session来追踪客户会话的。
而Cookie保管在客户端，不占用服务器资源。假如并发阅读的用户十分多，Cookie是很好的选择。关于Google、Baidu、Sina来说，Cookie或许是唯一的选择。
5、浏览器支持的不同
Cookie是需要客户端浏览器支持的。假如客户端禁用了Cookie，或者不支持Cookie，则会话跟踪会失效。关于WAP上的应用，常规的Cookie就派不上用场了。
假如客户端浏览器不支持Cookie，需要运用Session以及URL地址重写。需要注意的是一切的用到Session程序的URL都要进行URL地址重写，否则Session会话跟踪还会失效。关于WAP应用来说，Session+URL地址重写或许是它唯一的选择。
假如客户端支持Cookie，则Cookie既能够设为本浏览器窗口以及子窗口内有效（把过期时间设为–1），也能够设为一切阅读器窗口内有效（把过期时间设为某个大于0的整数）。但Session只能在本阅读器窗口以及其子窗口内有效。假如两个浏览器窗口互不相干，它们将运用两个不同的Session。（IE8下不同窗口Session相干）
6、跨域支持上的不同
Cookie支持跨域名访问，例如将domain属性设置为“.biaodianfu.com”，则以“.biaodianfu.com”为后缀的一切域名均能够访问该Cookie。跨域名Cookie如今被普遍用在网络中，例如Google、Baidu、Sina等。而Session则不会支持跨域名访问。Session仅在他所在的域名内有效。
仅运用Cookie或者仅运用Session可能完成不了理想的效果。这时应该尝试一下同时运用Cookie与Session。Cookie与Session的搭配运用在实践项目中会完成很多意想不到的效果。
#### Cookie的优缺点
优点：
+ 通过良好的编程，可以控制保存在Cookie中Session对象的大小
+ 通过加密和安全传输技术（SSL），减少Cookie破解的可能性
+ 只在Cookie保存不敏感的数据
+ 控制Cookie的生命周期，使之不会永久有效，偷盗者可能拿到一个过期的Cookie

缺点：
+ Cookie的长度和数量受限
+ Cookie仍存在安全性问题，即使加密也如此

---

**参考资料**
[Cookie 与 Session 的区别](https://juejin.im/entry/5766c29d6be3ff006a31b84e)
[看完就彻底懂了session和cookie](https://www.jianshu.com/p/25802021be63)