# 计数质数

### 题目：

```javascript
统计所有小于非负整数 n 的质数的数量。


示例 1：

    输入：n = 10
    输出：4
    解释：小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
    
示例 2：
    
    输入：n = 0
    输出：0
    
示例 3：

    输入：n = 1
    输出：0
```

思路：

一般逻辑，首先定义判断一个数是否是质数的函数，然后for循环处理n以内的数。

质数的定义— **素数**（Prime number），又称**质数**，指在大于1的[自然数](https://zh.wikipedia.org/wiki/%E8%87%AA%E7%84%B6%E6%95%B0)中，除了1和该数自身外，无法被其他自然数[整除](https://zh.wikipedia.org/wiki/%E6%95%B4%E9%99%A4)的数（也可定义为只有1与该数本身两个正因数的数）。规定1不是质数。

根据以上定义，来写一个判断函数：

```javascript
const isPrime = function(num){
    if(num<2){
        return false;
    }
    
    for(let i=2;i<num;i++){
        if(num%i==0){
            return false; 
        }
        return true;
    }
}
```

以上代码有个问题是，假如我想知道10000是不是质数，for循环就会从2一直循环到1000去，这样显然不太安逸，能不能有更优化的办法呢？

我们随便拿一个数举例 — 36，判断他是否是质数，结果发现：

```javascript
2  *  18 = 36
4  *  9  = 36
6  *  6  = 36    // √36 
18 *  2  = 36
9  *  4  = 36
```

通过上面的情况我们发现，以√36 为界，后面找到的36的约数，都是前面的镜像，也即是说我们只需要找2 - √num之间的数就可以了，于是我们优化一下：

```javascript
const isPrime = function(num){
    if(num<2){
        return false;
    }
    
    for(let i=2;i<Math.sqrt(num);i++){
        if(num%i==0){
            return false; 
        }
        return true;
    }
}
```





你以为到这里就结束了？ naive

接下来介绍一种——_找质数算法之埃拉托色尼筛选法_（Sieve of _Eratosthenes_算法）

算法思路：  
1）从质数2开始，所有2的倍数一定不是质数，根据这点我们可以把2的倍数全部摘除  
2）3也是质数，所有3的倍数也一定不是质数，把3的倍数全部摘除  
3）重复1）、2），每当碰到质数，就把该质数的所有倍数全部摘除，最后剩下的数就全是质数

![](../.gitbook/assets/1606932458-hgvonw-sieve_of_eratosthenes_animation.gif)

实现代码：

```javascript
const countPrimes = function(n){
    if(n.length < 2) return 0;
    const Prime = new Array(n).fill(true);
    for(let i=2; i*i < n; i++){
        if(Prime[i]){
            for(let j = i*i; j<n ; j+=i){
                Prime[j] = false;
            }
        }
    }
    let count = 0;
    for(let i=2;i<n;i++){
        if(Prime[i]){
            count++; 
        }
    }
    return count;
}
```

