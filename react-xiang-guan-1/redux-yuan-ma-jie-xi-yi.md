# Redux源码解析&lt;一&gt;

今天咱们来研究**redux**的源码，放一下**redux**的文件解构

![](../.gitbook/assets/image%20%2845%29.png)

这里借鉴一下[**wuming**](https://segmentfault.com/a/1190000016460366)大佬的思路，我们先从简单工具类`utils`开始读，先易后难

### 

## actionTypes.js

```javascript
const randomString = () =>
  Math.random()
    .toString(36)
    .substring(7)
    .split('')
    .join('.')

const ActionTypes = {
  INIT: `@@redux/INIT${randomString()}`,
  REPLACE: `@@redux/REPLACE${randomString()}`,
  PROBE_UNKNOWN_ACTION: () => `@@redux/PROBE_UNKNOWN_ACTION${randomString()}`
}

export default ActionTypes

```

actionTypes文件里定义了三个类型，这个先不过多介绍，主要说一下**randomString\(\)**方法

Math.random\(\)相当于生成一个随机Number对象，之后Number\(\).toString\(36\)可能大家就不太清楚了，其实toString\(radix\)是可以传参的，参数**radix**就是要转换的数字基数，比如toString\(2\)代表转化为2进制

所以这里radix的范围就是2到36， 36 =&gt; 10\(0-9个数字\) + a-z\(26个字母\)

![](../.gitbook/assets/image%20%2843%29.png)

### 

## isPlainObject.js

```javascript
/**
 * @param {any} obj The object to inspect.
 * @returns {boolean} True if the argument appears to be a plain object.
 */
export default function isPlainObject(obj) {
  if (typeof obj !== 'object' || obj === null) return false

  let proto = obj
  while (Object.getPrototypeOf(proto) !== null) {
    proto = Object.getPrototypeOf(proto)
  }

  return Object.getPrototypeOf(obj) === proto
}
```

这里就一个判断是否是对象的方法**isPlainObject**，看不太懂的同学可以去看我总结的[原型与原型链](../js-fu-zhi-zi-ding-yi-nei-rong/yuan-xing-yu-yuan-xing-lian.md)

```javascript
function MyObj (str){
    console.log('MyObj:',str);
}
var A = new MyObj('This is A');
var B = {name:'This is B'};
var C = new Object({
    name:'This is C'
})

isPlainObject(A)    //false
isPlainObject(B)    //true
isPlainObject(C)    //true
```

一句话总结**：凡不是new Object\(\)或者字面量的方式构建出来的对象都不是简单对象**

\*\*\*\*

## warning.js

```javascript
export default function warning(message) {
  /* eslint-disable no-console */
  if (typeof console !== 'undefined' && typeof console.error === 'function') {
    console.error(message)
  }
  /* eslint-enable no-console */
  try {
    // This error was thrown as a convenience so that if you enable
    // "break on all exceptions" in your console,
    // it would pause the execution at this line.
    throw new Error(message)
  } catch (e) {} // eslint-disable-line no-empty
}
```

这里就是一个简单的打印错误信息



现在开始上主菜

## index.js

```javascript
import createStore from './createStore'
import combineReducers from './combineReducers'
import bindActionCreators from './bindActionCreators'
import applyMiddleware from './applyMiddleware'
import compose from './compose'
import warning from './utils/warning'
import __DO_NOT_USE__ActionTypes from './utils/actionTypes'

/*
 * This is a dummy function to check if the function name has been altered by minification.
 * If the function has been minified and NODE_ENV !== 'production', warn the user.
 */
function isCrushed() {}

if (
  process.env.NODE_ENV !== 'production' &&
  typeof isCrushed.name === 'string' &&
  isCrushed.name !== 'isCrushed'
) {
  warning(
      /*
        抛出错误代码
      */
  )
}

export {
  createStore,
  combineReducers,
  bindActionCreators,
  applyMiddleware,
  compose,
  __DO_NOT_USE__ActionTypes
}
```

**index.js**是整个redux的入口文件，尾部的export出来的方法是不是都很熟悉，每个方法对应了一个js,这也是后面我们要分析的。这个有两个点需要讲一下：

**第一个，\_\_DO\_NOT\_USE\_\_ActionTypes。** 这个很陌生，平时在项目里面我们是不太会用到的，redux的官方文档也没有提到这个，如果你不看源码你可能就不知道这个东西的存在。这个干嘛的呢？我们一点一点往上找，找到这么一行代码：

```javascript
import __DO_NOT_USE__ActionTypes from './utils/actionTypes'
```

这个引入的js不就是我们之前分析的utils的其中一员吗？里面定义了redux自带的action的类型，从这个变量的命名来看，这是帮助开发者检查不要使用redux自带的action的类型，以防出现错误。

**第二个，函数isCrushed。** 这里面定义了一个函数isCrushed，但是函数体里面并没有东西。第一次看的时候很奇怪，为啥要这么干？相信有不少小伙伴们跟我有一样的疑问，继续往下看，紧跟着后面有一段代码：

```javascript
if (
  process.env.NODE_ENV !== 'production' &&
  typeof isCrushed.name === 'string' &&
  isCrushed.name !== 'isCrushed'
) {
  warning(
      /*
        抛出错误代码
      */
  )
}
```

看到process.env.NODE\_ENV，这里就要跟我们打包时用的环境变量联系起来。当process.env.NODE\_ENV==='production'这句话直接不成立，所以warning也就不会执行；当process.env.NODE\_ENV!=='production'，比如是我们的开发环境，我们不压缩代码的时候typeof isCrushed.name === 'string' && isCrushed.name !== 'isCrushed'也不会成立；当process.env.NODE\_ENV!=='production'，同样是我们的开发环境，我们进行了代码压缩，此时isCrushed.name === 'string' && isCrushed.name !== 'isCrushed'就成立了，可能有人不理解isCrushed函数不是在的吗？为啥这句话就不成立了呢？其实很好理解，了解过代码压缩的原理的人都知道，函数isCrushed的函数名将会被一个字母所替代，这里我们举个例子，我将redux项目的在development环境下进行了一次压缩打包。代码做了这么一层转换：

**未压缩**

```javascript
function isCrushed() {}
if (
  process.env.NODE_ENV !== 'production' &&
  typeof isCrushed.name === 'string' &&
  isCrushed.name !== 'isCrushed'
)
```

**压缩后**

```javascript
function d(){}"string"==typeof d.name&&"isCrushed"!==d.name
```

此时判断条件就成立了，错误信息就会打印出来。这个主要作用就是防止开发者在开发环境下对代码进行压缩

### 

## createStore.js

函数createStore接受三个参数（reducer、preloadedState、enhancer）

```javascript

export default function createStore(reducer, preloadedState, enhancer) {
    
    ...
    ...
    ...
    
  return {
    dispatch,
    subscribe,
    getState,
    replaceReducer,
    [$$observable]: observable
  }
}
```

reducer不多讲，我们主要讲一下_preloadedState_和_enhancer_

_preloadedState_是用来做初始化的

```javascript
let currentReducer = reducer  //存一份初始化的reducer
let currentState = preloadedState  //初始化状态
let currentListeners = []    //当前订阅列表
let nextListeners = currentListeners    //新的订阅列表
let isDispatching = false
```

_enhancer_中文翻译为增强器，顾名思义就是用来增强redux的

```javascript
if (typeof enhancer !== 'undefined') {
  if (typeof enhancer !== 'function') {
    throw new Error('Expected the enhancer to be a function.')
  }
  return enhancer(createStore)(reducer, preloadedState)
}
```

常见的enhancer就是redux-thunk以及redux-saga，一般都会配合applyMiddleware一起使用，而applyMiddleware的作用就是将这些enhancer格式化成符合redux要求的enhancer。具体applyMiddleware实现，下面我们将会讲到。我们先看redux-thunk的使用的例子：

```javascript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers/index';

const store = createStore(
  rootReducer,
  applyMiddleware(thunk)
);
```

看完上面的代码，可能会有人问“**createStore函数第二个参数不是preloadedState吗？这样不会报错吗？**” 首先肯定不会报错，毕竟官方给的例子，不然写个错误的例子也太大跌眼镜了吧！redux肯定是做了这么一层转换，我在createStore.js找到了这么一行代码：

```javascript
if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
 enhancer = preloadedState
 preloadedState = undefined
}
```

当第二个参数preloadedState的类型是Function的时候，并且第三个参数enhancer未定义的时候，此时preloadedState将会被赋值给enhancer，preloadedState会替代enhancer变成undefined的。有了这么一层转换之后，我们就可以大胆地第二个参数传enhancer了。



### dispatch

```javascript
function dispatch(action) {
  if (!isPlainObject(action)) {
      /*
        抛出错误代码
      */
  }

  if (typeof action.type === 'undefined') {
      /*
        抛出错误代码
      */
  }

  if (isDispatching) {
      /*
        抛出错误代码
      */
  }

  try {
    isDispatching = true
    currentState = currentReducer(currentState, action)
  } finally {
    isDispatching = false
  }

  const listeners = (currentListeners = nextListeners)
  for (let i = 0; i < listeners.length; i++) {
    const listener = listeners[i]
    listener()
  }

  return action
}
```

1. dispatch先判断action是否是对象
2. action.type是否是undefined
3. 判断isDispatching，是否有其他reducer在同时操作

isDispatching的作用是防止同时触发两个reducer操作，从而造成数据的紊乱

执行前isDispatching设置为true，阻止后续的action进来触发reducer操作，得到的state值赋值给currentState，完成之后再finally里将isDispatching再改为false，允许后续的action进来触发reducer操作。接着一一通知订阅者做数据更新，不传入任何参数。最后返回当前的action。



### getState

```javascript
function getState() {
  if (isDispatching) {
    throw new Error(
      /*
        抛出错误代码
      */
    )
  }

  return currentState
}
```

getState会先判断是否有reducer在同时操作，然后返回currentState

这个很好理解，当action触发reducer操作数据时，肯定不允许你同时读取数据



### subscribe

```javascript
function subscribe(listener) {
  if (typeof listener !== 'function') {
      /*
        抛出错误代码
      */
  }
  if (isDispatching) {
    throw new Error(
      /*
        抛出错误代码
      */
    )
  }

  let isSubscribed = true

  ensureCanMutateNextListeners()
  nextListeners.push(listener)

  return function unsubscribe() {
    if (!isSubscribed) {
      return
    }
    
    if (isDispatching) {
      /*
        抛出错误代码
      */
    }
    
    isSubscribed = false

    ensureCanMutateNextListeners()
    const index = nextListeners.indexOf(listener)
    nextListeners.splice(index, 1)
  }
}
```

1. 首先判断listener是否是function
2. 再判断是否有reducer正在执行

接下来执行了函数ensureCanMutateNextListeners，给nextListeners赋一份currentListeners的拷贝

```javascript
function ensureCanMutateNextListeners() {
  if (nextListeners === currentListeners) {
    nextListeners = currentListeners.slice()
  }
}
```

至于为什么一开始设置nextListeners = currentListeners，现在又要分开，我举个简单的栗子🌰

![](../.gitbook/assets/image%20%2840%29.png)

如果不分开那么他俩指向同一个引用，nextListeners的变化会引起currentListeners的变化，所以这里使用浅拷贝给nextListeners赋一个新值

之后把listener入栈nextListeners，并返回一个unsubscribe\(\)方法，用来解绑listener



#### replaceReducer、observable

这两个方法并不常用，这里暂时不讲



## combineReducers.js

combineReducers的作用是把多个reducer合成一个

```javascript
export default function combineReducers(reducers) {
  const reducerKeys = Object.keys(reducers)
  const finalReducers = {}
  for (let i = 0; i < reducerKeys.length; i++) {
    const key = reducerKeys[i]
    /*
      抛出错误代码
    */
    if (typeof reducers[key] === 'function') {
      finalReducers[key] = reducers[key]
    }
  }
  const finalReducerKeys = Object.keys(finalReducers)

  let unexpectedKeyCache
  if (process.env.NODE_ENV !== 'production') {
    unexpectedKeyCache = {}
  }

  let shapeAssertionError
  try {
    assertReducerShape(finalReducers)
  } catch (e) {
    shapeAssertionError = e
  }

  return function combination(state = {}, action) {
    ...
  }
}
```

实现逻辑并不复杂，主要关注几个变量finalReducers、finalReducerKeys

中间的assertReducerShape方法检测finalReducers里的每个reducer是否都有默认返回值

```javascript
function assertReducerShape(reducers) {
  Object.keys(reducers).forEach(key => {
    const reducer = reducers[key]
    const initialState = reducer(undefined, { type: ActionTypes.INIT })
    /*
      错误判断代码
    */
    if (
      typeof reducer(undefined, {
        type: ActionTypes.PROBE_UNKNOWN_ACTION()
      }) === 'undefined'
    ) {
      throw new Error(/*
        抛出错误
    */)
    }
  })
}
```

它主要做了以下检测：

1. 不能占用&lt;redux/\*&gt;的命名空间
2. 如果遇到未知的action的类型，不需要要用默认返回值

如果传入type为 **@@redux/INIT&lt;随机值&gt;** 的action，返回undefined，说明没有对未  
知的action的类型做响应，需要加默认值。如果对应type为 **@@redux/INIT&lt;随机值&gt;** 的action返回不为undefined,但是却对应type为 **@@redux/PROBE\_UNKNOWN\_ACTION\_&lt;随机值&gt;** 返回为undefined，说明占用了 &lt;redux/\*&gt; 命名空间。

最后返回**combination**函数

```javascript
return function combination(state = {}, action) {
    /*
      ... 一些错误判断代码
    */

    let hasChanged = false
    const nextState = {}
    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i]
      const reducer = finalReducers[key]
      const previousStateForKey = state[key]
      const nextStateForKey = reducer(previousStateForKey, action)
      if (typeof nextStateForKey === 'undefined') {
        const errorMessage = getUndefinedStateErrorMessage(key, action)
        throw new Error(errorMessage)
      }
      nextState[key] = nextStateForKey
      hasChanged = hasChanged || nextStateForKey !== previousStateForKey
    }
    return hasChanged ? nextState : state
  }
```

除开一些错误判断代码，这里首先定义了一个hasChanged变量用来表示state是否发生变化，然后遍历reducers集合，将每个reducer对应的原state传入其中，得出其对应的新的state。



## compose.js

compose方法也很简单

```javascript
export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```

分三种情况

1. funcs长度为0时，返回一个传入什么就返回什么的函数
2. funcs长度为1时，返回funcs\[0\]中的函数
3. funcs长度大于1时，返回一个reduce处理funcs中的函数

现在来分析下最后的reduce做了什么操作，首先复习一下reduce的用法

```javascript
const array = [1,2,3,4,5]
array.reduce((pre,current) =>{
    return pre+current
})
// 10
```

所以这里其实就是把funcs函数数组处理成**函数一元链式调用**

要注意的是，这里的逻辑会时函数数组从右到左执行

```javascript
funcs大概类似这样
const funcs = [ function A(){},function B(){},function B(){} ]
funcs.reduce((pre,current) => (...args) => pre(current(...args)) )
```

### 柯里化

可能还是有小伙伴看不懂，那我们来讲一下   \(\) =&gt; \(\) =&gt; \(\)  这种写法的意思

这种函数写法叫做[柯里化](https://baike.baidu.com/item/%E6%9F%AF%E9%87%8C%E5%8C%96/10350525?fr=aladdin)

举个栗子🌰

```javascript
// 普通函数
function add(x,y){
    return {
        x+y
    }
}

// 柯里化函数
function add(x){
    return function(y){
        x+y
    }
}
var add3 = new add(3)    // function(y){ 3+y }
add3(4) = 7
```

在计算机科学中，_柯里化_（Currying）是把接受多个参数的函数变换成接受一个单一参数\(最初函数的第一个参数\)的函数，并且返回接受余下的参数且返回结果的新函数的技术。



