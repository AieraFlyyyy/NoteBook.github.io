# Event Loop中的宏任务与微任务

## 开门见山

首先什么是eventloop，[Wikipedia](http://en.wikipedia.org/wiki/Event\_loop)这样定义：

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FYPjrl45dQkQBiQAVz8nt%2Ffile.jpeg?alt=media)

要了解Event Loop，首先要知道js在浏览器中是如何运行的。\
\
js在诞生之初，就是一个**单线程**的**脚本语言**。 \
&#x20;\
单线程的意思就是，同一时间只能有一条代码在执行，不会同时执行多条代码。\
于是就出现了异步的概念，也就是Event Loop所做的事情，打个比方：\
&#x20;     咱们去超市买东西的时候，收银员一次性只能接待一位顾客 ，那排队的一个个人也就是一个个准备执行的宏任务，在收钱的时候，顾客偶尔也会参加一些充值优惠、满减、换购活动，这些就是微任务，在这些宏任务（买东西）和微任务（参加活动） 都执行完毕之后，才能接待下一个客人（下一轮任务）\


举个例子，一道经典面试题：

```javascript
setTimeout(() => console.log(4))

new Promise(resolve => {
  resolve()
  console.log(1)
}).then(_ => {
  console.log(3)
})

console.log(2)
```

\
所以上述代码首先执行Promise中的**1**，然后遇到`Promise.then`是微任务，所以会先挂起，先执行最外部的**2**，本轮宏任务到此执行完毕，然后开始执行微任务：Promise.then()，所以输出**3**，setTimeout()作为下轮宏任务执行其内部逻辑。`setTimeout`就是作为宏任务来存在的，而`Promise.then`则是具有代表性的微任务，上述代码的执行顺序就是按照序号来输出的。\
`Promise()`在实例化过程中，内部所执行的代码相当于是同步的。

所以输出顺序为：\
1，2，3，4



接下来让咱们来总结一下宏任务和微任务分别有哪些

**宏任务：**

```javascript
I/O、
setTimeout、
setInterval、
setImmediate（浏览器暂时不支持，只有IE10支持，node支持，具体可见）、
requestAnimationFrame（浏览器独有）
```

**微任务：**

```javascript
Promise.then catch finally、
Process.nextTick（Node独有）、
MutationObserver（浏览器独有）
```



现在来两道题，看看自己有没有理解到位？

```javascript
new Promise((resolve,reject) =>{
    resolve();
    console.log('promise 1');
}).then(() =>{
    setTimeout(() =>{
        console.log('timeout 1');
    }, 2000);
    console.log('then 1');
})

new Promise((resolve,reject) =>{
    console.log('promise 2');
    resolve();
}).then(() =>{
    console.log('then 2');
    setTimeout(() =>{
        console.log('timeout 2');
    }, 0);
})
```

```javascript

setTimeout(() =>{
    new Promise((resolve,reject) =>{
        resolve();
        console.log('promise 1');
    }).then(() =>{
        console.log('then 1');
    })
    console.log('timeout 1');
}, 0);


new Promise((resolve,reject) =>{
    console.log('promise 2');
    resolve();
}).then(() =>{
    console.log('then 2');
    setTimeout(() =>{
        console.log('timeout 2');
    }, 100);
})
```

试试看能不能迅速说出结果来

我们数三二一公布结果



三

二

一



```javascript
第一题：
promise 1
promise 2
then 1
then 2
timeout 2
timeout 1


第二题：
promise 2
then 2
promise 1
timeout 1
then 1
timeout 2

```
