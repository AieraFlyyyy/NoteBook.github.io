# 关于react的setState

### **先说结论:** 1.setState是异步方法 2.在组建更新时，所有的setState会整合为一个setState更新 3.在setTimeout、setInterval中，setState是同步更新

从源码上了解原因:

#### **1.setState方法**

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F7gN638TQwFrCINqUs27Z%2Ffile.jpeg?alt=media)

这里的partialState会产生新的state以一种Object.assgin()的方式跟旧的state进行合并\
PS:**（这里分享个我看源码的小技巧，遇到不明白含义的变量，先把每个单词的意思查到， 理解了单词的意思再去看就会豁然开朗了，比如partialState,（partial是部分的意思），那么partialState很明显就代表一部分需要更新的state，这样看源码会比死记硬背容易得多）**

#### ****

#### **2.enqueueState （enqueue：入队）**

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FmjitClTW2tl9j7CSJNiX%2Ffile.jpeg?alt=media)

####

#### 3.enqueueUpdate

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FgUQyzHFMMUahkmh3IuOX%2Ffile.jpeg?alt=media)

**isBatchingUpdates**变量置为true，则会走批量更新分支，setState的更新会被存入队列中，待同步代码执行完后，再执行队列中的state更新。 \
**isBatchingUpdates** 为 true，则把当前组件（即调用了 setState 的组件）放入 **dirtyComponents** 数组中；否则 **batchUpdate** 所有队列中的更新

####

#### 4.batchingStrategy

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2Fqy3dDzu2MxHFOhlX1jou%2Ffile.jpeg?alt=media)

参考资料：[https://www.jianshu.com/p/89a04c132270](https://www.jianshu.com/p/89a04c132270)
