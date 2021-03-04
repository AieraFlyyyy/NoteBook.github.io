# React源码常用方法、全局变量&lt;二&gt;

### 1.expirationContext

保存创建`expirationTime`的上下文，在`syncUpdates`和`deferredUpdates`中分别被设置为`Sync`和`AsyncExpirationTime`，在有这个上下文的时候任何更新计算出来的过期时间都等于`expirationContext`。

比如调用`ReactDOM.flushSync`的时候，他接受的回调中的`setState`

### 2.nextUnitOfWork

用于记录`render`阶段`Fiber`树遍历过程中下一个需要执行的节点。

在`resetStack`中分别被重置

他只会指向`workInProgress`

3.



