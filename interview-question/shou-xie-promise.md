# 手写Promise

## 开门见山

#### 根据Promise的几个特点：

```javascript
Promise 核心实现 - 状态、then

Promise.resolve

Promise 异步实现

then 方法多次调用 

链式调用实现

promise.prototype.all

promise.prototype.finally

promise.prototype.catch
```

#### 全文我们分三个步骤来推导：一、满足基本逻辑；二、满足链式调用；三、其他

### 一、满足基本Promise逻辑

1.首先定义Promise的状态**STATUS**，因为**STATUS**必须是以下三种状态中的一种：**Pending、Fulfilled、Rejected**   
所以我们需要保证每种**STATUS**是唯一的 

```javascript
const STATUS = {
    PENDING: Symbol(),
    FULFILLED: Symbol(),
    REJECTED: Symbol(),
}
```

2.然后我们定义一下全局对象

```javascript
status = STATUS.PENDING;  // Promise的当前状态
value = undefined;        // 成功后的返回值
reason = undefined;       // 出错后返回的原因
```

3.接下来分别定义resolve、reject方法，并用constructor立即执行callback，目的是把resolve、reject函数传递给后续回调

```javascript
  // 定义resolve
  resolve = value => {
  
    // STATUS状态一旦改变，则不允许修改为其他状态
    if (this.status === STATUS.PENDING) {
      // 状态改为FULFILLED
      this.status = STATUS.FULFILLED;
      // 赋值
      this.value = value;
    }
  }
  
  // 定义reject
  reject = reason => {
  
    // STATUS状态一旦改变，则不允许修改为其他状态
    if (this.status === STATUS.PENDING) {

      // 状态改为REJECTED
      this.status = STATUS.REJECTED;
      // error赋值
      this.reason = reason;
    }
  }
  
  constructor(callback) { 
    //把resolve、reject方法传递给使用者
    callback(this.resolve, this.reject);   
  }
```

4.然后我们实现then方法

```javascript
then = (onFulfilled,onRejected) =>{
    // FULFILLED状态调用onFulfilled方法
    if(this.status === STATUS.FULFILLED){
        onFulfilled(this.value);
    }
    // REJECTED状态调用onRejected方法
    if(this.status === STATUS.REJECTED){
        onRejected(this.reason)
    }
}
```

第一阶段完整代码:

```javascript
const STATUS = {
    PENDING: Symbol(),
    FULFILLED: Symbol(),
    REJECTED: Symbol(),
}

class MyPromise{
    status = STATUS.PENDING;  // Promise的当前状态
    value = undefined;        // 成功后的返回值
    reason = undefined;       // 出错后返回的原因
    
    // 定义resolve
    resolve = value => {
    
      // STATUS状态一旦改变，则不允许修改为其他状态
      if (this.status === STATUS.PENDING) {
          // 状态改为FULFILLED
          this.status = STATUS.FULFILLED;
          // 赋值
          this.value = value;
      }
    }
    
    // 定义reject
    reject = reason => {
    
      // STATUS状态一旦改变，则不允许修改为其他状态
      if (this.status === STATUS.PENDING) {
          // 状态改为REJECTED
          this.status = STATUS.REJECTED;
          // error赋值
          this.reason = reason;
      }
  
    }
    
    constructor(callback) { 
      //把resolve、reject方法传递给使用者
      callback(this.resolve, this.reject);   
    }
    
    then = (onFulfilled,onRejected) =>{
        if(this.status === STATUS.FULFILLED){
            onFulfilled(this.value);
        }
        
        if(this.status === STATUS.REJECTED){
            onRejected(this.reason)
        }
    }
}
```

接下来测试一下效果：

```javascript
const promise = new Promise((resolve, reject) => {
  resolve('成功');
}).then(
  (data) => {
    console.log('success', data)
  },
  (err) => {
    console.log('faild', err)
  }
)
```

控制台输出：

`"success 成功"`

现在我们已经实现好了一个简洁版的Promise了～  但它仍然有很多问题存在，目前为止我们的MyPromise只能处理同步逻辑，如果我传入一个异步逻辑，会发生什么呢？

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('成功');
  },1000);
}).then(
  (data) => {
    console.log('success', data)
  },
  (err) => {
    console.log('faild', err)
  }
)
```

执行后可以发现，没有任何输出。  
因为当执行`MyPromise.then()`时，resolve还没有被调用，所以此时`STATUS`处于PENDING状态，导致不会有结果输出。

那么接下来，我们开始优化：

```javascript
class MyPromise{
    
    onFulfilledList = []; // 存放成功回调的列表
    onRejectedList = [];  // 存放失败回调的列表
    
    resolve = value => {
      if (this.status === STATUS.PENDING) {
          this.status = STATUS.FULFILLED;
          this.value = value;
    
          // 当onFulfilledList 列表中有成功回调时
          while (this.onFulfilledList.length) {
              //执行所有的成功回调
              this.onFulfilledList.shift()()    
          }
      }
    }
    
    reject = reason => {
      if (this.status === STATUS.PENDING) {
          this.status = STATUS.REJECTED;
          this.reason = reason;
    
          // 当onRejectedList 列表中有失败回调时
          while (this.onRejectedList.length) {
              //执行所有的失败回调
              this.onRejectedList.shift()()    
          }
      }
  
    }
    
    then = (onFulfilled,onRejected) =>{
        if(this.status === STATUS.FULFILLED){
            onFulfilled(this.value);
        }
        
        if(this.status === STATUS.REJECTED){
            onRejected(this.reason)
        }
        
        if(this.status === STATUS.PENDING){
            // 如果promise的状态是 pending，
            // 需要将 onFulfilled 和 onRejected 函数存放起来，
            // 等待状态确定后，再依次将对应的函数执行
            this.onFulfilledList.push(() => {
                onFulfilled(this.value)
            });
            this.onRejectedList.push(()=> {
                onRejected(this.reason);
            })
        }
    }
}
```

再测试一次

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('成功');
  },1000);
}).then(
  (data) => {
    console.log('success', data)
  },
  (err) => {
    console.log('faild', err)
  }
)
```

控制台输出：

`"success 成功"`

至此，咱们的MyPromise算是满足基本需求了～

### 二、满足链式调用

Promise的一个特点是可以执行很多个`.then()`，并且前一个then返回的值可以被后面所有then获取到（如果后面的then不做修改的话），这就是then的**链式调用**。

接下来我们一起分析一下如何实现：假如我们在then中返回一个新的Promise对象，然后把当前的值传给`Promise.then()`不就可以实现了吗。

首先我们需要写一个兼容方法来确定是否要返回链式还是返回值：

```javascript
then = (onFulfilled,onRejected) =>{
  if(this.status===FULFILLED){
    // 如果状态为FULFILLED，把value传给后面的方法
    const x = onFulfilled(this.value);
    // 该方法根据x的值是否为Promise对象，进行不同的处理
    resolvePromise(promise2, x, resolve, reject)
  }
}

const resolvePromise = (promise2, x, resolve, reject) => {
  // Promise2如果等于x，则直接报错，防止循环检索
  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected form promise #<Promise>'))
  }

  if (x instanceof MyPromise) {
    // 如果x还是Promise说明后面还有回调函数，则用then执行
    x.then(resolve, reject);
    } else {
    // 如果x是一个值，直接resolve()
    resolve(x);
  }
}
```

优化后的完整代码：​

```javascript
const STATUS = {
    PENDING: Symbol(),
    FULFILLED: Symbol(),
    REJECTED: Symbol(),
}

class MyPromise{
  status = STATUS.PENDING;  // 首先初始化状态为PENDING
  value = undefined;        // 初始化返回的值为undefined
  reason = undefined;       // 初始化出错原因为undefined


  onFulfilledList = []; // 存放成功回调的列表
  onRejectedList = [];  // 存放失败回调的列表
  
  resolve = value => {
    if (this.status === STATUS.PENDING) {
        this.status = STATUS.FULFILLED;
        this.value = value;
  
        // 当onFulfilledList 列表中有成功回调时
        while (this.onFulfilledList.length) {
            //执行所有的成功回调
            this.onFulfilledList.shift()()    
        }
    }
  }
  
  reject = reason => {
    if (this.status === STATUS.PENDING) {
        this.status = STATUS.REJECTED;
        this.reason = reason;
  
        // 当onRejectedList 列表中有失败回调时
        while (this.onRejectedList.length) {
            //执行所有的失败回调
            this.onRejectedList.shift()()    
        }
    }
  }
  
  constructor(callback) {
    callback(this.resolve, this.reject);    //把resolve、reject方法扔给后续回调
  }

  // 核心then方法
  then = (onFulfilled = () => { }, onRejected = () => { }) => {
    // 定义一个Promise方法，最后返回它
    const promise2 = new MyPromise((resolve, reject) => {
      if (this.status === STATUS.FULFILLED) {
        setTimeout(() => {
          try {
            // 如果状态为FULFILLED，把value传给后面的方法
            const x = onFulfilled(this.value);
            // 该方法根据x的值是否为Promise对象，进行不同的处理
            resolvePromise(promise2, x, resolve, reject)
          } catch (e) {
            reject(e)
          }
        }, 0)
      } else if (this.status === STATUS.REJECTED) {
        setTimeout(() => {
          try {
            // 如果状态为FULFILLED，把reason传给后面的方法
            const x = onRejected(this.reason)
            resolvePromise(promise2, x, resolve, reject)
          } catch (e) {
            reject(e);
          }
        }, 0)
      } else if (this.status === STATUS.PENDING) {
        // 如果状态为PENDING，给onFulfilled、onRejected列表都同时新增一条执行方法
        this.onFulfilledList.push(() => {
          setTimeout(() => {
            try {
              const x = onFulfilled(this.value);
              resolvePromise(promise2, x, resolve, reject)
            } catch (e) {
              reject(e)
            }
          }, 0)
        });
        this.onRejectedList.push(() => {
          setTimeout(() => {
            try {
              const x = onRejected(this.reason)
              resolvePromise(promise2, x, resolve, reject)
            } catch (e) {
              reject(e);
            }
          }, 0)
        });
      }
    })
    return promise2;
  }
}

const resolvePromise = (promise2, x, resolve, reject) => {
  // Promise2如果等于x，则直接报错，防止循环检索
  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected form promise #<Promise>'))
  }

  if (x instanceof MyPromise) {
    // 如果x还是Promise说明后面还有回调函数，则用then执行
    x.then(resolve, reject);
  } else {
    // 如果x是一个值，直接resolve()
    resolve(x);
  }
}
```

### 三、Promise的其他方法

现在我们可以来实现promise的其他方法了：

1.finally

```javascript
finally = (callback) => {
  return this.then(value => {
    return MyPromise.resolve(callback()).then(() => value)
  }, reason => {
    return MyPromise.resolve(callback()).then(() => {
      throw reason
    })
  })
}
```

2. all

```javascript
all = (array) =>{
  let result = [];
  let index = 0;

  return new MyPromise((resolve, reject) => {
    function addData(key, value) {
      result[key] = value;
      index++;

      // 如果current是一个值，则等到所有array中的方法执行完毕后，再resolve()
      if (index == array.length) {
        resolve(result)
      }
    }

    // 循环执行array中的方法
    for (let i = 0; i < array.length; i++) {
      const current = array[i];

      // 执行之前也是判断current对象，是否是Promise
      if (current instanceof MyPromise) {
        // 是则then，继续传递
        current.then(value => {
          addData(i, value);
        }, err => {
          // 有错误就reject
          reject(err)
        })
      } else {
        addData(i, array[i])
      }
    }
  })
}
```

3. race

```javascript
race = (promises) =>{
  return new Promise((resolve, reject) => {
    // 一起执行就是for循环
    for (let i = 0; i < promises.length; i++) {
      let val = promises[i];
      if (val && typeof val.then === 'function') {
        val.then(resolve, reject);
      } else { // 普通值
        resolve(val)
      }
    }
  });
}
```



