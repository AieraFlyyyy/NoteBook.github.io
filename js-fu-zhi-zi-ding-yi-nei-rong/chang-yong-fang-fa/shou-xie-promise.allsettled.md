# 手写Promise.allSettled

### 起因

公司项目有个初始化接口，使用Promise.all\(\)，会同时请求8个接口，且都是会对页面造成毁灭级影响的数据获取接口，这样会带来一旦某个接口出错，会导致Promise.all\(\)直接catah错误，让页面白屏

为了解决上述问题，我决定用Promise.allSettled来重构此接口。

### 

### 什么是Promise.allSettled?

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled)上说：

> 该**`Promise.allSettled()`**方法返回一个在所有给定的promise都已经`fulfilled`或`rejected`后的promise，并带有一个对象数组，每个对象表示对应的promise结果。

所以很明显，Promise.allSettled对比Promise.all，优势在于：它会返回所有promise的执行结果，不会因reject而中断



### 手写实现

```javascript
const MyAllSettled = (promiseArr) =>{
    return new Promise((resolve) =>{
        let resultArr=[];
        const len = Array.isArray(promiseArr)? promiseArr.length : 0;
        if(len===0) resolve([]);
        const compute = () => {
          if(--len === 0) { 
            resolve(resultArr)
          }
        }
        promiseArr.forEach((element,index) =>{
          // 判断传入的是否是 promise 类型
          if(element instanceof Promise) { 
            element
            .then(val, ()=> {
              resultArr[index] = { status: 'fulfilled', value: val}
              compute()
            })
            .catch(err, () =>{
              resultArr[index] = { status: 'rejected', reason: err }
              compute()
            })
          } else {
            args[index] = { status: 'fulfilled', value: value}
            compute()
          }
        })
    })
}
```



