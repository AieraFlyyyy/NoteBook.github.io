# 防抖节流函数



### 防抖函数：

防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。   
类似场景：用户**提交表单**时可能多次点击造成不必要的问题，则可以使用防抖函数避免不必要的接口重复调用。

```javascript
function debounce(fn,t){
    if(typeof fn !== 'object'){
        console.log('function type error');
        return;
    }
    let context = this;
    let arg = arguments;
    let timeout;
    
    if(timeout) clearTimeout(timeout);
    timeout = setTimeout(() =>{
        fn.apply(context,arg);
    },t);
}
```

### 

### 节流函数：

节流，就是在n秒内重复点击函数，函数也只会执行一次。   
类似场景：在**mousemove\(\)**中如果有一个执行函数，使用节流函数可以防止其多次执行减少资源浪费。

```javascript
function debounce(fn,t){
    if(typeof fn !== 'object'){
        console.log('function type error');
        return;
    }
    let context = this;
    let arg = arguments;
    let timeout;
    
    if(!timeout){
        setTimeout(() =>{
            timeout = null;
            fn.apply(context,arg);
        },t);
    }
}
```



