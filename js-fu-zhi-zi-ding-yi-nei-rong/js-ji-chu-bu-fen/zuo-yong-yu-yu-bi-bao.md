# 作用域与闭包

### 什么是作用域

> 作用域是在运行时代码中的某些特定部分中变量，函数和对象的可访问性。换句话说，作用域决定了代码区块中变量和其他资源的可见性。

![](../../.gitbook/assets/image%20%2873%29.png)

上图就是一个js文件以及其中的三个函数：A、B、C

A、B、C有着各自的执行上下文

```javascript
function A (){
    var a = 1;
    console.log(a)   // 1 
}

console.log(a)    // a is not defined
```

A的执行上下文也就是花括号括起来的部分

也就是说**A的作用域就在A的函数范围内**

我们可以这样理解：**作用域就是一个独立的地盘，让变量不会外泄、暴露出去**。也就是说**作用域最大的用处就是隔离变量，不同作用域下同名变量不会有冲突。**

值得注意的是：**块语句（大括号“｛｝”中间的语句），如 if 和 switch 条件语句或 for 和 while 循环语句，不像函数，它们不会创建一个新的作用域**。在块语句中定义的变量将保留在它们已经存在的作用域中。

```javascript
if (true) {
    // 'if' 条件语句块不会创建一个新的作用域
    var name = "Pika Chu"; // name 依然在全局作用域中
}
console.log(name); // 'Pika Chu'
```

所以为了防止这些语句中定义的变量污染全局环境，ES6引入了快级作用域，让变量的生命周期更可控





### 闭包

```javascript
function A (){
    var name = 'pika chu';
    function getName(){
        console.log(name)
    }
}
console.log(name) //name is not defined 
console.log(A.getName()) // pika chu
```

我们在函数A中定义了一个**name**，然后在外部打印**name**发现直接报错 `name is not defined`

调用A.getName\(\)，发现getName方法能够打印出**name**变量

这就是函数的闭包

> 一个函数和对其周围状态（**lexical environment，词法环境**）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是**闭包**（**closure**）。也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。

