# React源码常用方法、全局变量&lt;二&gt;

### 1.expirationContext

保存创建`expirationTime`的上下文，在`syncUpdates`和`deferredUpdates`中分别被设置为`Sync`和`AsyncExpirationTime`，在有这个上下文的时候任何更新计算出来的过期时间都等于`expirationContext`。

比如调用`ReactDOM.flushSync`的时候，他接受的回调中的`setState`

### 2.nextUnitOfWork

用于记录`render`阶段`Fiber`树遍历过程中下一个需要执行的节点。

在`resetStack`中分别被重置

他只会指向`workInProgress`

### 3.scheduleWorkToRoot

主要做了以下几个任务

* 找到当前`Fiber`的 root
* 给更新节点的父节点链上的每个节点的`expirationTime`设置为这个`update`的`expirationTime`，除非他本身时间要小于`expirationTime`
* 给更新节点的父节点链上的每个节点的`childExpirationTime`设置为这个`update`的`expirationTime`，除非他本身时间要小于`expirationTime`

最终返回 root 节点的`Fiber`对象

### 4.requestWork/addRootToSchedule

**requestWork**其实就是调用**addRootToSchedule**，再加上一些判断和处理，之后根据`expirationTime`调用`performSyncWork`还是`scheduleCallbackWithExpirationTime`

`addRootToSchedule` 的作用是把root加入到调度队列，并且如果ExpirationTime为NoWork或者小于root.ExpirationTime时，则会更新ExpirationTime。 addroottoschedule







