# 跨域解决方案

### 最经典的跨域方案jsonp

jsonp本质上是一个**Hack**，它利用&lt;scirpt&gt;标签不受同源策略限制的特性进行跨域操作。

**jsonp优点：**

* 实现简单
* 兼容性非常好

**jsonp的缺点：**

* 只支持get请求（因为&lt;scirpt&gt;标签只能get）
* 有安全性问题，容易遭受xss攻击
* 需要服务端配合jsonp进行一定程度的改造

### 

### 最流行的跨域方案cors

cors是目前主流的跨域解决方案，跨域资源共享\(CORS\) 是一种机制，它使用额外的 HTTP 头来告诉浏览器 让运行在一个 origin \(domain\) 上的Web应用被准许访问来自不同源服务器上的指定的资源。  
当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。如果你用express，可以这样在后端设置

  
最方便的跨域方案Nginx  
nginx是一款极其强大的web服务器，其优点就是轻量级、启动快、高并发。现在的新项目中nginx几乎是首选，我们用node或者java开发的服务通常都需要经过nginx的反向代理。  


### 其它跨域方案

1. HTML5 XMLHttpRequest 有一个API，postMessage\(\)方法允许来自不同源的脚本采用异步方式进行有限的通信，可以实现跨文本档、多窗口、跨域消息传递。
2. WebSocket 是一种双向通信协议，在建立连接之后，WebSocket 的 server 与 client 都能主动向对方发送或接收数据，连接建立好了之后 client 与 server 之间的双向通信就与 HTTP 无关了，因此可以跨域。
3. window.name + iframe：window.name属性值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值，我们可以利用这个特点进行跨域。
4. location.hash + iframe：a.html欲与c.html跨域相互通信，通过中间页b.html来实现。 三个页面，不同域之间利用iframe的location.hash传值，相同域之间直接js访问来通信。
5. document.domain + iframe： 该方式只能用于二级域名相同的情况下，比如 a.test.com 和 b.test.com 适用于该方式，我们只需要给页面添加 document.domain ='test.com' 表示二级域名都相同就可以实现跨域，两个页面都通过js强制设置document.domain为基础主域，就实现了同域。

