# 手写实现new

new一个函数时，发生了什么

1. 创建一个新的空的对象
2. 把这个对象链接到原型对象上
3. 这个对象被绑定为this
4. 如果这个函数不返回任何东西，那么就会默认return this

接下来分享三种new的实现方法

#### 第一种

```javascript
const myNew = (Con, ...args) => {
// 创建一个空对象
    const obj = {}
// 给这个对象指定原型链
    Object.setPrototypeOf(obj, Con.prototype)
    let result = Con.apply(obj, args)
    return result instanceof Object ? result : obj
}
```



#### 第二种

```javascript
function myNew(Cons,...args){ 
    let obj = {}; 
    obj.__proto__ = Cons.prototype; //执行原型链接 
    let res = Cons.call(obj,args); 
    return typeof res === 'object' ? res : obj; 
} 
```



#### 第三种

```javascript
function myNew () {
  //创建一个新对象
  let obj  = {};
  //获得构造函数
  let Con = [].shift.call(arguments);
  //链接到原型（给obj这个新生对象的原型指向它的构造函数的原型）
  obj.__proto__ = Con.prototype;
  //绑定this
  let result = Con.apply(obj,arguments);
  //确保new出来的是一个对象，因为Con执行的结果会返回当前实例
  return typeof result === "object" ? result : obj
}
```





