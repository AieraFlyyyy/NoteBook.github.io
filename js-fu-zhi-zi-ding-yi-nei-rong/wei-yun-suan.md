# 位运算符

## [赋值运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Assignment\_Operators)

### |=  [按位或赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise\_Operators#Bitwise\_OR)

```javascript
Operator: x |= y 
Meaning: x = x | y
```

对每一对比特位执行**或（OR）操作**。如果 a 或 b 为 1，则 `a OR b` 结果为 1。**或操作**的真值表：

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FNl2pJQ5jTMl0lZZgZCUJ%2Ffile.jpeg?alt=media)

**示例：**

```javascript
var bar = 5;
bar |= 2; // 7
// 5: 00000000000000000000000000000101
// 2: 00000000000000000000000000000010
// -----------------------------------
// 7: 00000000000000000000000000000111
```



### ^= [按位异或赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise\_Operators#Bitwise\_XOR)

```javascript
Operator: x ^= y 
Meaning: x = x ^ y
```

对每一对比特位执行**异或（XOR）操作**。当 a 和 b 不相同时，`a XOR b` 的结果为 1。**异或操作**真值表：

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FUoFLYjUHfTrIKH28VoQx%2Ffile.jpeg?alt=media)

**示例：**

```javascript
var bar = 5;
bar ^= 1; // 4
// 5: 00000000000000000000000000000101
// 1: 00000000000000000000000000000001
// -----------------------------------
// 4: 00000000000000000000000000000100
```



### &= [按位与赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise\_Operators#Bitwise\_AND)

```javascript
Operator: x &= y 
Meaning:  x  = x & y
```

对每对比特位执行**与（AND）操作** 。只有 a 和 b 都是 1 时，`a AND b` 才是 1。**与操作**的真值表如下：

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2Fxo4dRxfjpNfjLO5noQeY%2Ffile.jpeg?alt=media)

示例：

```javascript
var bar = 5;
bar ^= 1; // 4
var bar = 5;
bar &= 2; // 0
// 5: 00000000000000000000000000000101
// 2: 00000000000000000000000000000010
// -----------------------------------
// 0: 00000000000000000000000000000000
```

###

### >>>= [无符号右移赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise\_Operators#Unsigned\_right\_shift)

```javascript
Operator: x >>>= y 
Meaning:  x    = x >>> y
```

该操作符会将第一个操作数向右移动指定的位数。向右被移出的位被丢弃，左侧用0填充。因为符号位变成了 0，所以结果总是非负的。（译注：即便右移 0 个比特，结果也是非负的。） \
对于非负数，有符号右移和无符号右移总是返回相同的结果。例如 `9 >>> 2` 和 `9 >> 2` 一样返回 2：

**示例：**

```javascript
var bar = 5; //   (00000000000000000000000000000101)
bar >>>= 2;  // 1 (00000000000000000000000000000001)

var bar = -5; // (-00000000000000000000000000000101)
bar >>>= 2; // 1073741822 (00111111111111111111111111111110)
```



### >>= [右移赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise\_Operators#Right\_shift)

```javascript
Operator: x >>= y 
Meaning:  x   = x >> y
```

该操作符会将第一个操作数向右移动指定的位数。向右被移出的位被丢弃，拷贝最左侧的位以填充左侧。由于新的最左侧的位总是和以前相同，符号位没有被改变。所以被称作“符号传播”。

**示例：**

```javascript
9 >> 2 得到 2:
     9 (base 10): 00000000000000000000000000001001 (base 2)
                  --------------------------------
9 >> 2 (base 10): 00000000000000000000000000000010 (base 2) = 2 (base 10)
```

相比之下， `-9 >> 2` 得到 -3，因为符号被保留了。

```javascript
     -9 (base 10): 11111111111111111111111111110111 (base 2)
                   --------------------------------
-9 >> 2 (base 10): 11111111111111111111111111111101 (base 2) = -3 (base 10)
```



### <<= [左移赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise\_Operators#Left\_shift)

```javascript
Operator: x <<= y 
Meaning:  x   = x << y
```

该操作符会将第一个操作数向左移动指定的位数。向左被移出的位被丢弃，右侧用 0 补充。

**示例：**

```javascript
var bar = 5; //  (00000000000000000000000000000101)
bar <<= 2; // 20 (00000000000000000000000000010100)
```



### _\~\~_  转换数字类型

该方法等价于_Math.floor()_

示例：

```javascript
~~1.32 =  1
~~[] = 0
~~true = 1
~~false =0
~~'' = 0
```

\~是按位非，就是每一位取反（处理时，会先将数据转成二进制，在进行取反操作）

按位非之后再接一个按位非，就转换为数字了

