# 阻止冒泡和捕获

**事件流**\
****\
****事件流描述的是从页面中接收事件的顺序，比如有两个嵌套的div，点击了内层的div，这时候是内层的div先触发click事件还是外层先触发？目前主要有三种模型：\
\
&#x20;    1\. 事件冒泡：事件开始时由最具体的元素接收，然后逐级向上传播到较为不具体的元素\
&#x20;    2\. 事件捕获：不太具体的节点更早接收事件，而最具体的元素最后接收事件，和事件冒泡相反\
&#x20;    3\. DOM事件流：DOM2级事件规定事件流包括三个阶段，事件捕获阶段，处于目标阶段，事件冒泡阶段，首先发生的是事件捕获，为截取事件提供机会，然后是实际目标接收事件，最后是冒泡阶段\
&#x20;    4\. Opera、Firefox、Chrome、Safari都支持DOM事件流，IE不支持事件流，只支持事件冒泡\
\
\
事件冒泡\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FyeSYaA7bJwxZfddEFBzC%2Ffile.jpeg?alt=media)\
事件从最下层向上传递，依次触发父元素的该事件处理函数。\
\
事件捕获\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FqPl2Rstl3V2dPWzRh6PT%2Ffile.jpeg?alt=media)\
从最上层元素，直到最下层（你点击的那个target）元素。路过的所有节点都可以捕捉到该事件。\
\
DOM流事件\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FeHC5vJBhmkHbHv85XO78%2Ffile.jpeg?alt=media)\
\
event.preventDefault()\
&#x20;     用于阻止元素的默认行为。\
&#x20;     比如\<a>标签默认行为是转跳或锚点定位，\<form>表单有submit事件等等。\
\
event.stopPropagation()\
&#x20;     用于阻止事件向上冒泡传递，阻止任何父事件处理程序被执行。\
\
React事件流 ：[https://github.com/youngwind/blog/issues/107](https://github.com/youngwind/blog/issues/107) 跟原生有些许区别\
图片参考：[https://zhuanlan.zhihu.com/p/111256841](https://zhuanlan.zhihu.com/p/111256841) 知乎—JS事件\
\
\
下面记录一个案例：\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FuOLFQT4tuiCTeGFhLoT3%2Ffile.jpeg?alt=media)

```javascript
<div>
   <span>复制<span>
</div>
```

结构如上，父元素绑定了一个打开Image的方法，子元素一个可以复制并弹出提示的方法。\
需求是，点击除了“复制” 外的其他触发父元素事件，点击“复制”只触发子元素事件。\
\
着手解决：使用event.preventDefault()阻止冒泡。\
\
遇到问题：父元素事件仍然可以触发。\
\
最后解决：使用e.target对象做对比，看是否是目标元素。\
\
