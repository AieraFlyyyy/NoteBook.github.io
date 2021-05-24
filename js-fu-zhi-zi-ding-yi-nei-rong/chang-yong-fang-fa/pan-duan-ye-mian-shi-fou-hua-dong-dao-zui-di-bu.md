# 判断页面是否滑动到最底部

scrollTop为滚动条在Y轴上的滚动距离。

clientHeight为内容可视区域的高度。

scrollHeight为内容可视区域的高度加上溢出（滚动）的距离。

滚动条到底部的条件即为scrollTop + clientHeight == scrollHeight。

```javascript
// 获取scrollTop，为滚动条在Y轴上的滚动距离。
const getScrollTop = () =>{
　　var scrollTop = 0, bodyScrollTop = 0, documentScrollTop = 0;
　　if(document.body){
　　　　bodyScrollTop = document.body.scrollTop;
　　}
　　if(document.documentElement){
　　　　documentScrollTop = document.documentElement.scrollTop;
　　}
    scrollTop = (bodyScrollTop - documentScrollTop > 0) ? bodyScrollTop : documentScrollTop;
    return scrollTop;
}

// 获取scrollHeight，为内容可视区域的高度加上溢出（滚动）的距离。
const getScrollHeight = () =>{
    var scrollHeight = 0;
    var bSH,dSH;
    if(document.body){
  　    bSH = document.body.scrollHeight;
    }
    if(document.documentElement){
　　　   dSH = document.documentElement.scrollHeight;
    }
    scrollHeight = (bSH - dSH > 0) ? bSH : dSH ;
    return scrollHeight;
}

// clientHeight为内容可视区域的高度。
const getClientHeight= () =>{
　  var clientHeight = 0;
　　if(document.compatMode === "CSS1Compat"){
　　　　clientHeight = document.documentElement.clientHeight;
　　}else{
　　　　clientHeight = document.body.clientHeight;
　　}
　　return clientHeight;
}
window.onscroll = function(){
    if(getScrollTop() + getClientHeight() === getScrollHeight()){
  	  console.log("已经到最底部了！!");  
    }
};
```

内容引用：[简书](https://www.jianshu.com/p/0a3aebd63a14)

