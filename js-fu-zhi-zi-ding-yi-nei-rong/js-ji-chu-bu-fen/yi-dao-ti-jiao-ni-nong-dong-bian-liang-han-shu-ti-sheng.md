---
description: 关于我文章都快写完了，又被我手贱点了删除这件事。。。
---

# 一道题教你弄懂变量/函数提升

这星期遇到一道巨难的题，看完直接给我整懵逼了

```javascript
function Foo(){
    getName = function(){ console.log(1) };
    return this;
}

Foo.getName = function(){ console.log(2) }

Foo.prototype.getName = function(){ console.log(3) };

var getName = function(){ console.log(4) };

function getName(){ console.log(5) }; 


Foo.getName();    
getName();           
Foo().getName();      
getName();           
new Foo.getName();    
new Foo().getName();   
new new Foo().getName(); 
```

。。。？？？

![](../../.gitbook/assets/image%20%2867%29.png)

原谅我菜，一开始我是真没想到正确的思路

但经过我一番冷（tou）静（kan）思（da）考（an）

这道题终于被我解出来了！

**解这道题的前置知识：**首先我们要搞懂什么是变量提升/函数提升



### Hoisting（变量提升）

根据[MDN](https://developer.mozilla.org/zh-CN/docs/Glossary/Hoisting)的说法：

> 变量提升（Hoisting）被认为是， Javascript中执行上下文 （特别是创建和执行阶段）工作方式的一种认识。在 [ECMAScript® 2015 Language Specification](https://www.ecma-international.org/ecma-262/6.0/index.html) 之前的JavaScript文档中找不到变量提升（Hoisting）这个词。不过，需要注意的是，开始时，这个概念可能比较难理解，甚至恼人。
>
> 例如，从概念的字面意义上说，“变量提升”意味着变量和函数的声明会在物理层面移动到代码的最前面，但这么说并不准确。实际上变量和函数声明在代码里的位置是不会动的，而是在编译阶段被放入内存中。

**变量提升的两个重要特点：**

* **只有声明被提升，初始化不会被提升**
* **声明会被提升到当前作用域的顶端**

例子1：只有声明被提升，初始化不会被提升

```javascript
console.log(foo);
var foo = 'foo';

// 预编译后
===========================>

var foo;
console.log(foo);    //undefined
foo = 'foo';
```

例子2：声明会被提升到当前作用域的顶端

```javascript
function hoistVariable(){
    if(!foo){
        foo = 'foo';
    }
    console.log(foo);
}
hoistVariable();

// 预编译后
===========================>

function hoistVariable(){
    var foo;
    if(!foo){        // !foo 为true
        foo = 'foo';
    }
    console.log(foo);
}
hoistVariable();    //foo
```



### 函数提升

#### 函数提升的两个重要特点：

* **函数声明和初始化都会被提升**
* **函数表达式不会被提升**

例子1：函数声明和初始化都会提升

```javascript
console.log(hoistFunction());
function hoistFunction(){
    console.log('hoistFunction');
}

// 预编译后
===========================>

function hoistFunction(){
    console.log('hoistFunction');
}
console.log(hoistFunction());    //hoistFunction
```

例子2：函数表达式不会被提升

```javascript
function hoistFunction() {
  foo();
  var foo = function() {
    console.log(1);
  };
  foo();
  function foo() {
    console.log(2);
  }
  foo();
}
hoistFunction();


// 预编译后
===========================>


function hoistFunction() {
  var foo;
  function foo() {
    console.log(2);
  }
  foo();  //2
  foo = function() {
    console.log(1);
  };
  foo();  //1
  foo();  //1
}
hoistFunction();
```

学废这两个概念的同学，我们再看一遍开头的难题

```javascript
function Foo(){
    getName = function(){ console.log(1) };
    return this;
}
Foo.getName = function(){ console.log(2) }
Foo.prototype.getName = function(){ console.log(3) };
var getName = function(){ console.log(4) };
function getName(){ console.log(5) }; 

// 预编译后
===========================>

var getName;
function getName(){ console.log(5) }; 
// *1
function Foo(){
    getName = function(){ console.log(1) };
    return this;
}
Foo.getName = function(){ console.log(2) }
Foo.prototype.getName = function(){ console.log(3) };
getName = function(){ console.log(4) };
// *2


Foo.getName();            //2
getName();                //4
Foo().getName();          
//*3
getName();                          
//*4

new Foo.getName();
//*5      
new Foo().getName();   
// *6
new new Foo().getName(); 
// *7 
```

**\*1**这里，变量名和函数名一致的情况下，函数优先原则，getName赋值为`getName(){ console.log(5) }`

**\*2**代码执行到这里，getName会赋值为`getName(){ console.log(4) }`

所以我们能得出27行的getName\(\)输出为4

### \*3

要搞懂**\*3**，我要先给大家介绍下运算符执行顺序优先级

![](../../.gitbook/assets/image%20%2868%29.png)

可以看到 =&gt;  `. [] ()`  这三个运算符的优先级处于所有运算符之最，当程序中有他们三位存在时，他们三位的执行优先级处于最高位

```javascript
Foo().getName()
```

所以此处会先计算Foo\(\)，在Foo\(\)函数执行完毕后，再执行.getName\(\)，也就是 

> Foo\(\).getName\(\)  ===&gt;   \(Foo\(\)\).getName\(\)

```javascript
function Foo(){
    getName = function(){ console.log(1) };
    return this;
}
```

这时，大家一定要注意！！Foo\(\)方法中，是没有定义getName变量的，但是它内部却在给getName赋值

也就是说，由于[闭包](zuo-yong-yu-yu-bi-bao.md#bi-bao)的特性，当Foo\(\)中没有getName变量时，它会在外层作用域中找getName，然后执行

`getName = function(){ console.log(1) }` 的赋值操作

所以，外部作用域的getName变量因为Foo\(\)方法的执行，而被赋值为`function(){ console.log(1) }`

同时我们注意到Foo\(\)方法return了个**this**，也就是外部执行上下文，于是我们得到

> Foo\(\).getName\(\)  ===&gt;   \(\(Foo\(\)\).getName\)\(\)  ===&gt;  this.getName\(\)

由此我们同时得到了**\*3**和**\*4**的输出

```javascript

function getName(){ console.log(5) }; 
// *1
function Foo(){
    getName = function(){ console.log(1) };
    return this;
}
Foo.getName = function(){ console.log(2) }
Foo.prototype.getName = function(){ console.log(3) };
getName = function(){ console.log(1) };    // 此处由于Foo()方法执行而改为1
// *2


Foo.getName();            //2
getName();                //4

Foo().getName();          //1      
//*3
getName();                //1
//*4

new Foo.getName();
//*5      
new Foo().getName();   
// *6
new new Foo().getName(); 
// *7 
```

### **\*5**

我们先按运算优先级优化下

> new Foo.getName\(\)  ===&gt;  new \(Foo.getName\)\(\) **** ===&gt; new \(function\(\){ console.log\(2\) }\)\(\)

所以**\*5**输出2

### \*6

我们按运算优先级优化，new Foo\(\).getName\(\)，由于**.**运算优先级最高会最先执行，然而\(\).getName这样的执行顺序并能运行。所以前面的new Foo\(\)会作为一个整体先执行，由此我们得到如下变化：

> new Foo\(\).getName\(\)  ===&gt;  \(new Foo\(\)\).getName\(\)

new Foo\(\)是把Foo作为构造函数，进行一个实例化操作，所以这里求的是实例对象的getName\(\)

> new Foo\(\).getName\(\)  ===&gt;  \(new Foo\(\)\).getName\(\)  ===&gt;  foo.getName\(\)

哈，没错，终于到了喜闻乐见的原型链环节辣！

由于实例化出来的对象中是没有getName方法的，由于[原型链](yuan-xing-yu-yuan-xing-lian.md#yuan-xing-lian)的特性，这里会从实例对象的原型上找getName方法

```javascript
Foo.prototype.getName = function(){ console.log(3) };
```

所以我们得到\*6输出3

### \*7

根据上面\*6的运算优先级优化经验，**\*7**就很简单啦

> new new Foo\(\).getName\(\)  ===&gt;  new \(new Foo\(\).getName\)\(\)  ===&gt;  new \(function\(\){ console.log\(3\) }\)\(\)

所以\*7也会输出3



### 最终输出

```javascript
function Foo(){
    getName = function(){ console.log(1) };
    return this;
}

Foo.getName = function(){ console.log(2) }

Foo.prototype.getName = function(){ console.log(3) };

var getName = function(){ console.log(4) };

function getName(){ console.log(5) }; 


Foo.getName();                //2
getName();                    //4
Foo().getName();              //1
getName();                    //1
new Foo.getName();            //2
new Foo().getName();          //3
new new Foo().getName();      //3
```

