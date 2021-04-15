---
description: '大佬笔记，咱理解到了就赶紧记下来 https://github.com/mqyqingfeng/Blog/issues/2'
---

# 原型与原型链

### 讲原型和原型链之前先明确几个概念：

```javascript
①只要函数才有 prototype    // function a (){}
②只有对象才有 _proto_      // var b = new a()
③只有原型才有 constructor  // a.prototype.constructor
```

大家一定要熟记于心的几个名词代表意思：

1. **函数/构造函数**   =&gt;   function a \(\) {}
2. **对象/实例对象**   =&gt;   var b = new a\(\)
3. **原型**                     =&gt;   a.prototype / b.\_proto\_

好的下面我们正式开始

### prototype

首先我们先看一个例子：

```javascript
function Func() {

}
// 再次强调: prototype是函数才会有的属性'①
Func.prototype.name = 'this is a Func';
var example1 = new A();
var example2 = new A();
console.log(example.name)     // this is a Func
console.log(example2.name)    // this is a Func
```

我们定义了构造函数 **Func**

让Func.prototype.name = 'this is a Func' ，给A的prototype指向的对象赋予一个name

然后我们定义了两个实例对象 example1&example2，并打印他俩的name，发现都是 'this is a Func'

### \_proto\_

再看一个栗子：

```javascript
function Func() {

}
var example = new Func();
console.log(example.__proto__ === Func.prototype); // true
```

### constructor

```javascript
function Func() {

}
console.log(Func === Func.prototype.constructor); // true
```

### 什么是原型？

你可以这样理解：每一个JavaScript对象\(null除外\)在创建的时候就会与之关联另一个对象，**这个对象就是我们所说的原型**，每一个对象都会从原型"继承"属性。  


### 总结关系

函数**Func**的 **prototype** 属性指向了一个对象，这个对象正是调用该构造函数而创建的**实例**的原型，也就是第一个例子中的 **example1** 和 **example2** 的原型。

翻译一下

```javascript
Func.prototype       =>  指向一个对象，假设它为 Obj
Obj.constructor      =>  Func
new Func()           =>  example1/example2
example1._proto_     =>  Obj

Func.prototype = example1._proto_ = Obj

Obj.constructor = Func
```

我们用一个简单的图来尽量演示一下它们之间的关系

![](../.gitbook/assets/image%20%2852%29.png)

### 

### 原型的原型

前面有说到，**原型也是一个对象**，那么我们就可以用最原始的方式创建它，那就是：

```javascript
var Obj = new Object();
Obj.name = 'This is Obj'
console.log(Obj.name) // This is Obj
```

![](../.gitbook/assets/image%20%2844%29.png)

### 原型链

那 Object.prototype 的原型呢？

null，我们可以打印：

```text
console.log(Object.prototype.__proto__ === null) // true
```

然而 null 究竟代表了什么呢？

引用阮一峰老师的 [《undefined与null的区别》](https://link.zhihu.com/?target=http%3A//www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html) 就是：

> null 表示“没有对象”，即该处不应该有值。

所以 Object.prototype.\_\_proto\_\_ 的值为 null 跟 Object.prototype 没有原型，其实表达了一个意思。

所以查找属性的时候查到 Object.prototype 就可以停止查找了。

所以最终的关系图就是

![](../.gitbook/assets/image%20%2842%29.png)

