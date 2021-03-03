# React源码常用方法、全局变量&lt;一&gt;

### 1.getContextForSubtree

用来获取context

### 2.createUpdate

创建一个update对象

```javascript
export const UpdateState = 0;
export const ReplaceState = 1;
export const ForceUpdate = 2;
export const CaptureUpdate = 3;

export function createUpdate(
  expirationTime: ExpirationTime,
  suspenseConfig: null | SuspenseConfig,
): Update<*> {
  return {
    //更新的过期时间
    expirationTime,
    suspenseConfig,

    // export const UpdateState = 0;
    // export const ReplaceState = 1;
    // export const ForceUpdate = 2;
    // export const CaptureUpdate = 3;

    //重点提下CaptureUpdate，在React16后有一个ErrorBoundaries功能
    //即在渲染过程中报错了，可以选择新的渲染状态（提示有错误的状态），来更新页面
    tag: UpdateState, //0更新 1替换 2强制更新 3捕获性的更新

    //更新内容，比如setState接收的第一个参数
    payload: null,

    //对应的回调，比如setState({}, callback )
    callback: null,

    //指向下一个更新
    next: null,

    //指向下一个side effect
    nextEffect: null,
  };
}
```

### 3.computeExpirationForFiber

为fiber对象计算expirationTime

```javascript
export function computeExpirationForFiber(
  currentTime: ExpirationTime,
  fiber: Fiber,
  suspenseConfig: null | SuspenseConfig,
): ExpirationTime {
  ...
  // Compute an expiration time based on the Scheduler priority.
    switch (priorityLevel) {
      case ImmediatePriority:
        expirationTime = Sync;
        break;
      case UserBlockingPriority:
        // TODO: Rename this to computeUserBlockingExpiration
        //一个是计算交互事件（如点击）的过期时间
        expirationTime = computeInteractiveExpiration(currentTime);
        break;
      case NormalPriority:
      case LowPriority: // TODO: Handle LowPriority
        // TODO: Rename this to... something better.
        //一个是计算异步更新的过期时间
        expirationTime = computeAsyncExpiration(currentTime);
        break;
      case IdlePriority:
        expirationTime = Never;
        break;
      default:
        invariant(false, 'Expected a valid priority level');
    }
  ...
}
```

### 4.computeExpirationBucket 、 computeAsyncExpiration、computeInteractiveExpiration

computeExpirationBucket是用来计算ExpirationTime的，根据传入参数的不同，得到不同大小的（用来区分优先级）ExpirationTime，具体可在[ExpirationTime](https://492467237.gitbook.io/notebook/react-xiang-guan-1/expirationtime#expirationtime-%E5%85%AC%E5%BC%8F)中详细查看。

```javascript
const UNIT_SIZE = 10
const MAGIC_NUMBER_OFFSET = 2

function computeExpirationBucket(
  currentTime,
  expirationInMs,
  bucketSizeMs,
): ExpirationTime {
  return (
    MAGIC_NUMBER_OFFSET +
    ceiling(
      currentTime - MAGIC_NUMBER_OFFSET + expirationInMs / UNIT_SIZE,
      bucketSizeMs / UNIT_SIZE,
    )
  )
}
```

```javascript

export const LOW_PRIORITY_EXPIRATION = 5000
export const LOW_PRIORITY_BATCH_SIZE = 250

// 计算异步ExpirationTime
export function computeAsyncExpiration(
  currentTime: ExpirationTime,
): ExpirationTime {
  return computeExpirationBucket(
    currentTime,
    LOW_PRIORITY_EXPIRATION,
    LOW_PRIORITY_BATCH_SIZE,
  )
}

export const HIGH_PRIORITY_EXPIRATION = __DEV__ ? 500 : 150
export const HIGH_PRIORITY_BATCH_SIZE = 100

// 计算交互的ExpirationTime
export function computeInteractiveExpiration(currentTime: ExpirationTime) {
  return computeExpirationBucket(
    currentTime,
    HIGH_PRIORITY_EXPIRATION,
    HIGH_PRIORITY_BATCH_SIZE,
  )
}
```

### 5.nextFlushedExpirationTime

//  直翻：下次刷新过期时间

这个是在`fiber-scheduler`中的一个全局变量，用来记录下一个需要渲染的`FiberRoot`的过期时间。注意他筛选的是整个应用中所有`FiberRoot`的优先级（是的，你没看错，应该 React 应用是可以有多个`FiberRoot`，比如你执行两次`ReactDOM.render`），并不关心每个`FiberRoot`子树的优先级。

他是在`findHighestPriorityRoot`中被赋值的，会遍历`firstScheduleRoot -> lastScheduledRoot`链表中所有`root`，并找到优先级最高（也就是`expirationTime`最小）的那个`root`进行赋值，并安排渲染

### 6.childExpirationTime

每次一个节点调用`setState`或者`forceUpdate`都会产生一个更新并且计算一个`expirationTime`，那么这个节点的`expirationTime`就是当时计算出来的值，**因为这个更新本身就是由这个节点产生的**

最终因为 React 的更新需要从`FiberRoot`开始，所以会执行一次向上遍历找到`FiberRoot`，而向上遍历则正好是一步步找到**创建更新的节点的父节点**的过程，这时候 React 就会对每一个该节点的父节点链上的节点设置`childExpirationTime`，因为这个更新是他们的子孙节点造成的

也就是说`childExpirationTime`是当前节点对父节点设置的ExpirationTime，它有以下两个特点：

* 同一个节点产生的连续两次更新，最红在父节点上只会体现一次`childExpirationTime`
* 不同子树产生的更新，最终体现在跟节点上的是优先级最高的那个更新

![](../.gitbook/assets/image%20%2821%29.png)

以上图为例，App作为父节点有四个child节点，其中`child2`的ExpirationTime**优先级**最高，所以`App`的ExpirationTime也会是`sync`

### 7.pendingTime

在`FiberRoot`上有两个值`earliestPendingTime`和`lastestPedingTime`，他们是一对值，**用来记录所有子树中需要进行渲染的更新的`expirationTime`的区间**

**通过追踪最大和最小值，React 可以判断在当前更新之后是否还具有优先级更低的任务需要执行（当前过期时间处理这两个值之间）**  


![](../.gitbook/assets/image%20%2822%29.png)

### **8.**suspendedTime

同样的在`ReactFiber`上有两个值`earliestSuspendedTime`和`lastestSuspendedTime`，**这两个值是用来记录被挂起的任务的过期时间的**

首先我们定义一下什么情况下任务是被挂起的：

* 出现可捕获的错误并且还有优先级更低的任务的情况下
* 当捕获到`thenable`，并且需要设置`onTimeout`的时候

我们称这个任务被`suspended`\(挂起\)了。记录这个时间主要是在`resolve`了`promise`之后，判断被挂起的组件更新是否依然处于目前已有的`suspenedTime`中间，如果不是的话是需要重新计算一个新的过期时间，然后从新加入队列进行调度更新的。

### 9.root.expirationTime 和 root.nextExpirationTimeToWorkOn

`root.expirationTime`是用来标志当前渲染的过期时间的，请注意他只管本渲染周期，他并不管你现在的渲染目标是哪个，渲染目标是由`root.nextExpirationTimeToWorkOn`来决定的。

他们的主要区别在于发挥作用的阶段

`expirationTime`作用于**调度阶段**，主要指责是：

* 决定是异步执行渲染还是同步执行渲染
* 作为`react-scheduler`的`timeout`标准，决定是否要优先渲染

`nextExpirationTimeToWorkOn`主要作用于**渲染阶段**：

* 决定那些更新要在当前周期中被执行
* 通过跟每个节点的`expirationTime`比较决定该节点是否可以直接`bailout`（跳过）

他们都是通过`pendingTime`、`suspenededTime`和`pingedTime`中删选出来的，唯一的不同是，`nextExpirationTimeToWorkOn`在没有`pending`或者`pinged`的任务的时候会选择最晚的`suspendedTime`，而`expirationTime`会选择最早的

###  10.currentSchedulerTime & currentRendererTime

这两个时间是用来是用来记录当前时间的，在**计算过期时间**和**比较任务是否过期**的时候都会用到`currentRendererTime`，`currentSchedulerTime`大部分时候都是等于`currentRendererTime`的，那为什么要设置两个时间呢？

这就是因为`batchedUpdates`了，在这种情况下如果同时创建了多个更新是会为每次更新计算过期时间的，而计算是要花时间的，如果每次都是请求当前时间，那么同一个`batch`中的不同更新得到的过期时间就会是不一样的，所以在一个`batch`中获取的当前时间应该是一样的，所以就设置了这么一个值，在我们请求当前时间的方法中有这么一段代码：

```javascript
findHighestPriorityRoot()
if (
  nextFlushedExpirationTime === NoWork ||
  nextFlushedExpirationTime === Never
) {
  recomputeCurrentRendererTime()
  currentSchedulerTime = currentRendererTime
  return currentSchedulerTime
}
```

`findHighestPriorityRoot`会设置`nextFlushedExpirationTime`，也就是只有在当前没有等待中的更新的情况下，才会重新计算当前时间。



