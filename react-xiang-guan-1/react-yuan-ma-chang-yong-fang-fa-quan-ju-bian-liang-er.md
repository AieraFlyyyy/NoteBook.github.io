# React源码常用方法、全局变量&lt;二&gt;

### 1.expirationContext

保存创建`expirationTime`的上下文，在`syncUpdates`和`deferredUpdates`中分别被设置为`Sync`和`AsyncExpirationTime`，在有这个上下文的时候任何更新计算出来的过期时间都等于`expirationContext`。

比如调用`ReactDOM.flushSync`的时候，他接受的回调中的`setState`

### 2.nextUnitOfWork

用于记录`render`阶段`Fiber`树遍历过程中下一个需要执行的节点。

在`resetStack`中分别被重置

他只会指向`workInProgress`

### 3.scheduleWorkToRoot\(\)

主要做了以下几个任务

* 找到当前`Fiber`的 root
* 给更新节点的父节点链上的每个节点的`expirationTime`设置为这个`update`的`expirationTime`，除非他本身时间要小于`expirationTime`
* 给更新节点的父节点链上的每个节点的`childExpirationTime`设置为这个`update`的`expirationTime`，除非他本身时间要小于`expirationTime`

最终返回 root 节点的`Fiber`对象

```javascript
function scheduleWorkToRoot(fiber: Fiber, expirationTime): FiberRoot | null {
  // Update the source fiber's expiration time
  if (
    fiber.expirationTime === NoWork ||
    fiber.expirationTime > expirationTime
  ) {
    fiber.expirationTime = expirationTime
  }
  let alternate = fiber.alternate
  if (
    alternate !== null &&
    (alternate.expirationTime === NoWork ||
      alternate.expirationTime > expirationTime)
  ) {
    alternate.expirationTime = expirationTime
  }
  // Walk the parent path to the root and update the child expiration time.
  let node = fiber.return
  if (node === null && fiber.tag === HostRoot) {
    return fiber.stateNode
  }
  while (node !== null) {
    alternate = node.alternate
    if (
      node.childExpirationTime === NoWork ||
      node.childExpirationTime > expirationTime
    ) {
      node.childExpirationTime = expirationTime
      if (
        alternate !== null &&
        (alternate.childExpirationTime === NoWork ||
          alternate.childExpirationTime > expirationTime)
      ) {
        alternate.childExpirationTime = expirationTime
      }
    } else if (
      alternate !== null &&
      (alternate.childExpirationTime === NoWork ||
        alternate.childExpirationTime > expirationTime)
    ) {
      alternate.childExpirationTime = expirationTime
    }
    if (node.return === null && node.tag === HostRoot) {
      return node.stateNode
    }
    node = node.return
  }
  return null
}
```

### 4.requestWork\(\)/addRootToSchedule\(\)

**requestWork**其实就是调用**addRootToSchedule**，再加上一些判断和处理，之后根据`expirationTime`调用`performSyncWork`还是`scheduleCallbackWithExpirationTime`

`addRootToSchedule` 的作用是把root加入到调度队列，并且如果ExpirationTime为NoWork或者小于root.ExpirationTime时，则会更新ExpirationTime，最后会将root赋值到全局的firstScheduledRoot和lastScheduledRoot中

```javascript
function requestWork(root: FiberRoot, expirationTime: ExpirationTime) {
  addRootToSchedule(root, expirationTime)
  if (isRendering) {
    // Prevent reentrancy. Remaining work will be scheduled at the end of
    // the currently rendering batch.
    return
  }

  if (isBatchingUpdates) {
    // Flush work at the end of the batch.
    if (isUnbatchingUpdates) {
      nextFlushedRoot = root
      nextFlushedExpirationTime = Sync
      performWorkOnRoot(root, Sync, true)
    }
    return
  }

  if (expirationTime === Sync) {
    performSyncWork()
  } else {
    scheduleCallbackWithExpirationTime(root, expirationTime)
  }
}

function addRootToSchedule(root: FiberRoot, expirationTime: ExpirationTime) {
  // Add the root to the schedule.
  // Check if this root is already part of the schedule.
  if (root.nextScheduledRoot === null) {
    // This root is not already scheduled. Add it.
    root.expirationTime = expirationTime
    if (lastScheduledRoot === null) {
      firstScheduledRoot = lastScheduledRoot = root
      root.nextScheduledRoot = root
    } else {
      lastScheduledRoot.nextScheduledRoot = root
      lastScheduledRoot = root
      lastScheduledRoot.nextScheduledRoot = firstScheduledRoot
    }
  } else {
    // This root is already scheduled, but its priority may have increased.
    const remainingExpirationTime = root.expirationTime
    if (
      remainingExpirationTime === NoWork ||
      expirationTime < remainingExpirationTime
    ) {
      // Update the priority.
      root.expirationTime = expirationTime
    }
  }
}
```

### 5.performWork\(\)

performWork中立马会执行findHighestPriorityRoot这个函数，这个函数的目的就是将firstScheduledRoot赋值到nextFlushedRoot，找到下一个需要操作的`root`

紧接着就会将nextFlushedRoot传入`performWorkOnRoot`当中。

### 6.performWorkOnRoot\(\)

这里也分为同步和异步两种情况，但是这两种情况的区别其实非常小。

首先是一个参数的区别，`isYieldy`在同步的情况下是`false`，而在异步情况下是`true`。这个参数顾名思义就是_是否可以中断_，那么这个区别也就很好理解了。

第二个区别就是在`renderRoot`之后判断一下`shouldYeild`，如果时间片已经用完，则不直接`completeRoot`，而是等到一下次`requestIdleCallback`之后再执行。

`renderRoot`和`completeRoot`分别对应两个阶段：

* 渲染阶段
* 提交阶段

渲染阶段可以被打断，而提交阶段不能

```javascript
function performWorkOnRoot(
  root: FiberRoot,
  expirationTime: ExpirationTime,
  isExpired: boolean,
) {
  isRendering = true

  if (deadline === null || isExpired) {
    let finishedWork = root.finishedWork
    if (finishedWork !== null) {
      // This root is already complete. We can commit it.
      completeRoot(root, finishedWork, expirationTime)
    } else {
      root.finishedWork = null
      const timeoutHandle = root.timeoutHandle
      if (enableSuspense && timeoutHandle !== noTimeout) {
        root.timeoutHandle = noTimeout
        cancelTimeout(timeoutHandle)
      }
      const isYieldy = false
      renderRoot(root, isYieldy, isExpired)
      finishedWork = root.finishedWork
      if (finishedWork !== null) {
        // We've completed the root. Commit it.
        completeRoot(root, finishedWork, expirationTime)
      }
    }
  } else {
    // Flush async work.
    let finishedWork = root.finishedWork
    if (finishedWork !== null) {
      // This root is already complete. We can commit it.
      completeRoot(root, finishedWork, expirationTime)
    } else {
      root.finishedWork = null
      // If this root previously suspended, clear its existing timeout, since
      // we're about to try rendering again.
      const timeoutHandle = root.timeoutHandle
      if (enableSuspense && timeoutHandle !== noTimeout) {
        root.timeoutHandle = noTimeout
        // $FlowFixMe Complains noTimeout is not a TimeoutID, despite the check above
        cancelTimeout(timeoutHandle)
      }
      const isYieldy = true
      renderRoot(root, isYieldy, isExpired)
      finishedWork = root.finishedWork
      if (finishedWork !== null) {
        if (!shouldYield()) {
          // Still time left. Commit the root.
          completeRoot(root, finishedWork, expirationTime)
        } else {
          root.finishedWork = finishedWork
        }
      }
    }
  }

  isRendering = false
}
```

### 7.findHighestPriorityRoot\(\)

一般情况下我们的 React 应用只会有一个`root`，所以这里的大部分逻辑其实都不是常见情况

循环`firstScheduledRoot => lastScheduledRoot`，`remainingExpirationTime`是`root.expirationTime`，也就是最早的过期时间。

如果他是`NoWork`说明他已经没有任务了，从链表中删除。

从剩下的中找到`expirationTime`最小的也就是优先级最高的`root`然后把他赋值给`nextFlushedRoot`并把他的`expirationTime`赋值给`nextFlushedExpirationTime`这两个公共变量。

```javascript
function findHighestPriorityRoot() {
  let highestPriorityWork = NoWork
  let highestPriorityRoot = null
  if (lastScheduledRoot !== null) {
    let previousScheduledRoot = lastScheduledRoot
    let root = firstScheduledRoot
    while (root !== null) {
      const remainingExpirationTime = root.expirationTime
      if (remainingExpirationTime === NoWork) {
        // This root no longer has work. Remove it from the scheduler.

        // TODO: This check is redudant, but Flow is confused by the branch
        // below where we set lastScheduledRoot to null, even though we break
        // from the loop right after.
        invariant(
          previousScheduledRoot !== null && lastScheduledRoot !== null,
          'Should have a previous and last root. This error is likely ' +
            'caused by a bug in React. Please file an issue.',
        )
        if (root === root.nextScheduledRoot) {
          // This is the only root in the list.
          root.nextScheduledRoot = null
          firstScheduledRoot = lastScheduledRoot = null
          break
        } else if (root === firstScheduledRoot) {
          // This is the first root in the list.
          const next = root.nextScheduledRoot
          firstScheduledRoot = next
          lastScheduledRoot.nextScheduledRoot = next
          root.nextScheduledRoot = null
        } else if (root === lastScheduledRoot) {
          // This is the last root in the list.
          lastScheduledRoot = previousScheduledRoot
          lastScheduledRoot.nextScheduledRoot = firstScheduledRoot
          root.nextScheduledRoot = null
          break
        } else {
          previousScheduledRoot.nextScheduledRoot = root.nextScheduledRoot
          root.nextScheduledRoot = null
        }
        root = previousScheduledRoot.nextScheduledRoot
      } else {
        if (
          highestPriorityWork === NoWork ||
          remainingExpirationTime < highestPriorityWork
        ) {
          // Update the priority, if it's higher
          highestPriorityWork = remainingExpirationTime
          highestPriorityRoot = root
        }
        if (root === lastScheduledRoot) {
          break
        }
        if (highestPriorityWork === Sync) {
          // Sync is highest priority by definition so
          // we can stop searching.
          break
        }
        previousScheduledRoot = root
        root = root.nextScheduledRoot
      }
    }
  }

  nextFlushedRoot = highestPriorityRoot
  nextFlushedExpirationTime = highestPriorityWork
}
```



### 8.requestHostCallback\(\)

开始进入调度，设置调度的内容，用`scheduledHostCallback`和`timeoutTime`这两个全局变量记录回调函数和对应的过期时间

调用`requestAnimationFrameWithTimeout`，其实就是调用`requestAnimationFrame`在加上设置了一个`100ms`的定时器，防止`requestAnimationFrame`太久不触发。

调用回调`animtionTick`并设置`isAnimationFrameScheduled`全局变量为`true`

### 9.animationTick\(\)

只要`scheduledHostCallback`还在就继续调用`requestAnimationFrameWithTimeout`，因为这一帧渲染完了可能队列还没情况，本身也是要进入再次调用的，这边就省去了`requestHostCallback`在次调用的必要性

接下去一段代码是用来计算相隔的`requestAnimationFrame`的时差的，这个时差如果连续两次都小于当前的`activeFrameTime`，说明平台的帧率是很高的，这种情况下会动态得缩小帧时间。

最后更新`frameDeadline`，然后如果没有触发`idleTick`则发送消息 







