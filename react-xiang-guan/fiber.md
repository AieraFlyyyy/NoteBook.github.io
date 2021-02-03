# Fiber

### **Fiber是什么**

React团队为了优化react卡顿的动画表现，重构了React的核心算法—[reconciliation](https://reactjs.org/docs/reconciliation.html) 。发布在react v16版本，之后为了区分优化前后的两个reconciler方法，优化之前的方法叫**stack reconciler**，之后的方法叫**fiber reconciler**简称Fiber。

### 

### 卡顿原因

Stack reconciler的工作流程很像函数的调用过程。父组件里调子组件，可以类比为函数的递归（这也是为什么被称为stack reconciler的原因）。在setState后，react会立即开始reconciliation过程，从父节点（Virtual DOM）开始遍历，以找出不同。将所有的Virtual DOM遍历完成后，reconciler才能给出当前需要修改真实DOM的信息，并传递给renderer，进行渲染，然后屏幕上才会显示此次更新内容。对于特别庞大的vDOM树来说，reconciliation过程会很长\(x00ms\)，在这期间，主线程是被js占用的，因此任何交互、布局、渲染都会停止，给用户的感觉就是页面被卡住了。

![](../.gitbook/assets/image%20%2818%29.png)

### 

### Scheduler

scheduling\(调度\)是fiber reconciliation的一个过程，主要决定应该在何时做什么。👆的过程表明在stack reconciler中，reconciliation是“一气呵成”，对于函数来说，这没什么问题，因为我们只想要函数的运行结果，但对于UI来说还需要考虑以下问题：

* 并不是所有的state更新都需要立即显示出来，比如屏幕之外的部分的更新
* 并不是所有的更新优先级都是一样的，比如用户输入的响应优先级要比通过请求填充内容的响应优先级更高
* 理想情况下，对于某些高优先级的操作，应该是可以打断低优先级的操作执行的，比如用户输入时，页面的某个评论还在reconciliation，应该优先响应用户输入

所以理想状况下reconciliation的过程应该是像下图所示一样，每次只做一个很小的任务，做完后能够“喘口气儿”，回到主线程看下有没有什么更高优先级的任务需要处理，如果又则先处理更高优先级的任务，没有则继续执行\([cooperative scheduling 合作式调度](https://www.w3.org/TR/requestidlecallback/)\)。  


![](../.gitbook/assets/image%20%2817%29.png)

### 

### 任务拆分 fiber-tree & fiber

先看一下stack-reconciler下的react是怎么工作的。代码中创建（或更新）一些元素，react会根据这些元素创建（或更新）Virtual DOM，然后react根据更新前后virtual DOM的区别，去修改真正的DOM。注意，**在stack reconciler下，DOM的更新是同步的，也就是说，在virtual DOM的比对过程中，发现一个instance有更新，会立即执行DOM操作**。  


![](../.gitbook/assets/image%20%2819%29.png)

而fiber-conciler下，操作是可以分成很多小部分，并且可以被中断的，所以同步操作DOM可能会导致fiber-tree与实际DOM的不同步。对于每个节点来说，其不光存储了对应元素的基本信息，还要保存一些用于任务调度的信息。因此，fiber仅仅是一个对象，表征reconciliation阶段所能拆分的最小工作单元，和上图中的react instance一一对应。通过`stateNode`属性管理Instance自身的特性。通过child和sibling表征当前工作单元的下一个工作单元，return表示处理完成后返回结果所要合并的目标，通常指向父节点。整个结构是一个链表树。每个工作单元（fiber）执行完成后，都会查看是否还继续拥有主线程时间片，如果有继续下一个，如果没有则先处理其他高优先级事务，等主线程空闲下来继续执行。

![](../.gitbook/assets/image%20%2820%29.png)

```javascript
fiber {
  	stateNode: {},
    child: {},
    return: {},
    sibling: {},
}
```





ps：以上知识点以及图片搬运 - [https://juejin.cn/post/6844903582622285831](https://juejin.cn/post/6844903582622285831) 想了解更多可去详情查看

