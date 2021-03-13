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

### 5.requestHostCallback

开始进入调度，设置调度的内容，用`scheduledHostCallback`和`timeoutTime`这两个全局变量记录回调函数和对应的过期时间

调用`requestAnimationFrameWithTimeout`，其实就是调用`requestAnimationFrame`在加上设置了一个`100ms`的定时器，防止`requestAnimationFrame`太久不触发。

调用回调`animtionTick`并设置`isAnimationFrameScheduled`全局变量为`true`

### 6.animationTick

只要`scheduledHostCallback`还在就继续调用`requestAnimationFrameWithTimeout`，因为这一帧渲染完了可能队列还没情况，本身也是要进入再次调用的，这边就省去了`requestHostCallback`在次调用的必要性

接下去一段代码是用来计算相隔的`requestAnimationFrame`的时差的，这个时差如果连续两次都小鱼当前的`activeFrameTime`，说明平台的帧率是很高的，这种情况下会动态得缩小帧时间。

最后更新`frameDeadline`，然后如果没有触发`idleTick`则发送消息







