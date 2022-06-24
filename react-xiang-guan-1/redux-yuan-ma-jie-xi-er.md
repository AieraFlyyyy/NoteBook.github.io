# Redux源码解析<二>

承接上一部，我们继续来讲redux源码

## applyMiddleware.js

```javascript
export default function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    const store = createStore(...args)
    let dispatch = () => {
      throw new Error(
        `Dispatching while constructing your middleware is not allowed. ` +
          `Other middleware would not be applied to this dispatch.`
      )
    }

    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args)
    }
    const chain = middlewares.map(middleware => middleware(middlewareAPI))
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
    }
  }
}
```

这里return之后接了个初始化调用，可能很多同学看不明白，那让我调整一下写法

```javascript
return (createStore) => (...args) => {
  const store = createStore(...args)
  ...
  return {
    ...store,
    dispatch
  }
}
```

hjh，原来就是个柯里化函数罢了：当时我看的时候也被它唬住了

不明白柯里化函数的同学可以看我[上一篇文章](redux-yuan-ma-jie-xi-yi.md#ke-li-hua)末尾的分析



下面我们完整走一遍applyMiddleware的逻辑

引用实例：

```javascript
import {createStore,combineReducers,applyMiddleware} from 'redux';
import logger from 'redux-logger'
import thunk from 'redux-thunk';
import * as reducers from './reudcer';

const middlewares = [thunk];

if(process.env.NODE_ENV === 'development'){
    middlewares.push(looger);
}

export const allReducers = combineReducers(reducers);

export const store = createStore(
    allReducers,
    applyMiddleware(...middlewares)
)
```

&#x20;这里把applyMiddleware(...middlewares)当作第二参数传给**createStore**

[之前](redux-yuan-ma-jie-xi-yi.md#createstore-js)我们讲过，根据createStore的内部逻辑，当preloadedState为function，enhancer为undefined时，会把他俩互换

```javascript
if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
    enhancer = preloadedState
    preloadedState = undefined
}
```

现在再来看一下enhancer的调用逻辑

```javascript
if (typeof enhancer !== 'undefined') {
  if (typeof enhancer !== 'function') {
    throw new Error('Expected the enhancer to be a function.')
  }

  return enhancer(createStore)(reducer, preloadedState)
}
```

这里把两个参数传给enhancer，一个是createStore方法本身，另一个是替换后的preloadedState(值为undefined)

然后我们再来看applymiddleware的逻辑

```javascript
return (createStore) => (...args) => {
  const store = createStore(...args)
  let dispatch = () => {
    throw new Error(
      `Dispatching while constructing your middleware is not allowed. ` +
        `Other middleware would not be applied to this dispatch.`
    )
  }

  const middlewareAPI = {
    getState: store.getState,
    dispatch: (...args) => dispatch(...args)
  }
  const chain = middlewares.map(middleware => middleware(middlewareAPI))
  dispatch = compose(...chain)(store.dispatch)

  return {
    ...store,
    dispatch
  }
}
```

现在我们就明白这个store是从哪来的了

接着讲，初始化store之后定义了一个会抛出错误的dispatch，这里是为了防止有在代码执行途中调用dispatch的行为

然后初始化middlewareAPI

用map改造middlewares之后，放在chain里

用compose方法处理chain之后，覆盖掉会报错的dispatch

最后返回 `...store` 和改造后的dispatch

compose[前文](redux-yuan-ma-jie-xi-yi.md#compose-js)介绍过，它会把传进来的函数数组变为一元链式调用函数，并按照从右到左的顺序执行

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

可以看到，所谓执行middleware，其实也就是将原来的dispatch函数替换为会_遍历执行所有middleware_的新的dispatch函数。那么在执行完并return后，在我们的代码中拿到的store.dispatch函数就是经过修改的新的dispatch函数了，结果就是我们每次调用dispatch都会走一遍所有middlewares。

\


## bindActionCreators.js

```javascript
function bindActionCreator(actionCreator, dispatch) {
  return function() {
    return dispatch(actionCreator.apply(this, arguments))
  }
}

export default function bindActionCreators(actionCreators, dispatch) {
  if (typeof actionCreators === 'function') {
    return bindActionCreator(actionCreators, dispatch)
  }

  if (typeof actionCreators !== 'object' || actionCreators === null) {
    throw new Error(
      `bindActionCreators expected an object or a function, instead received ${
        actionCreators === null ? 'null' : typeof actionCreators
      }. ` +
        `Did you write "import ActionCreators from" instead of "import * as ActionCreators from"?`
    )
  }

  const keys = Object.keys(actionCreators)
  const boundActionCreators = {}
  for (let i = 0; i < keys.length; i++) {
    const key = keys[i]
    const actionCreator = actionCreators[key]
    if (typeof actionCreator === 'function') {
      boundActionCreators[key] = bindActionCreator(actionCreator, dispatch)
    }
  }
  return boundActionCreators
}
```

bindActionCreators的逻辑也不复杂，无非就是把传入的actionCreator导入到dispatch中去，最后返回一个action或者action集合





到此，redux源码解析就算完成啦！

这是我第一次尝试整体解析源码，希望能给大家带来一点收获～
