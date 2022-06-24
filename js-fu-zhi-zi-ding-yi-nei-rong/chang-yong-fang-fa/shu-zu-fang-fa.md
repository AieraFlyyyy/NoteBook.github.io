# 数组方法

### 一、不会改变原数组：

**1.map()**\
对数组的每一个元素调用一种方法

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F5xPk48d0rAxZdYodWb4Y%2Ffile.jpeg?alt=media)\


**2.filter()**\
匹配数组中的每一项，将满足条件的项作为新数组返回![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2Fs8ryL7SlDuJ1enHxiZae%2Ffile.jpeg?alt=media)\


**3.every()**\
对数组的每一项进行判断返回布尔值，如果所有元素都满足则返回true，否则返回false

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FLrZrvEUnpegZZUfYQfqc%2Ffile.jpeg?alt=media)\


**4.some()**\
对数组的每一项进行判断返回布尔值，如果有其中一个满足则返回true，否\
则返回false

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FD9LqtSIOZa40CYehuwfg%2Ffile.jpeg?alt=media)\


**5.reduce((pre,cur,index,array) => {})、**\
**reduceRight((pre,cur,index,array) => {})**\
reduce()方法从左到右对数组的每一项调用方法，返回最终结果reduceRight()方法是从右到左对数组的每一项调用方法，返回最终结果

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2Fa3rZVeKs7gGozR5XXBJf%2Ffile.jpeg?alt=media)\


**6.concat()**\
将两个数组进行拼接，返回新数组，不会改变原数组![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FLjRm60uronDUdWPKC3cf%2Ffile.jpeg?alt=media)\


**7.Array.from(arraylike)**\
用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FQ8QuwK9CqvtWB3If6JwJ%2Ffile.jpeg?alt=media)\


**8.Array.of()**\
该方法用于将一组值，转换为数组。

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F1DQDcnDDr6ixXWfg4gIN%2Ffile.jpeg?alt=media)\


**9.find((item,index,arr) => xxx)**\
该方法用于找出数组中第一个符合条件的元素，如过没有找到则返回undefined，他的参数是一个回调函数\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F8uKJbKM5ZCogOeigZlos%2Ffile.jpeg?alt=media)\


**10.findIndex()**\
该方法与find方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FX4venZ1yK3NLXTdUVLSs%2Ffile.jpeg?alt=media)\


**11.entries()、keys()、values()**\
这三个方法用法类似，都是返回一个遍历器对象，可以用for...of循环进行遍历，他们的区别是： keys()是对键名的遍历、values()是对键值的遍历、entries()是对键值对的遍历

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FBkUbBB0YSWc5W0aBBjZt%2Ffile.jpeg?alt=media)\


**12.includes(item,index)**\
该方法返回一个布尔值，表示某个数组中是否包含给定的值，该方法的第二个参数表示搜索的起始位置，默认为0

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FZ3JvHGxrKa0yWZYlWpoH%2Ffile.jpeg?alt=media)\


**13.flat()**\
有时数组中的成员还是数组，flat()方法就是用来将嵌套的数组“拉平”的flat()默认只会拉平一层，如果要拉平多层，可以通过传参定义，如果不知道有多少层，可以传Infinity

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FmgQ4zFir4jFkvIsUrY3F%2Ffile.jpeg?alt=media)\


**14.flatMap()**\
该方法会对数组的每个成员执行一个函数，相当于map()。然后对返回值组成的数组执行flat()方法，该方法返回一个新数组。\
flatMap()默认只能展开一层\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FCEkp2mrvgs6WTTCRU0cA%2Ffile.jpeg?alt=media)\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F2Gs7qcs3qr09UHlpsR5n%2Ffile.jpeg?alt=media)\


**15.slice()**\
该方法返回从原数组中指定开始下标到结束下标之间的项组成的新数组![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F2RGLdVXOApFbyQAxFy72%2Ffile.jpeg?alt=media)

###

### 二、会改变原数组：

**1.forEach()**\
对数组的每一个元素调用一种方法，forEach中拿到的v只是元素的复制，要想改变原数组，必须改变arr

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F9qfY5YaTMHFpqYTFrGKF%2Ffile.jpeg?alt=media)\


**2.push()**\
在数组最后一项后面再添加一项，返回新数组的长

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F6qDJqcQsHgSA9xmMX8UB%2Ffile.jpeg?alt=media)\


**3.pop()**\
删除数组的最后一项，返回值为被删除的那一项

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FmNArdcv6yqf2ToYMJeph%2Ffile.jpeg?alt=media)\


**4.shift()**\
删除数组的第一项，返回值为被删除的那一项

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2Fjb3Qv96gNYYdDBlwzGUP%2Ffile.jpeg?alt=media)\


**5.unshift()**\
在数组第一项前再多加一项或多项，返回值为新数组的长度

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FCspzsH9KwlFA0LrjU6YN%2Ffile.jpeg?alt=media)\


**6.splice(index,num,item)**\
对数组进行增删改操作，返回值为被修改的对象index代表修改的下标num=0 ，item != null 代表新增num=1 ，item !=null 代表修改num=1 ，item =null 代表删除

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FDGr17ikqhHReSAAv0ob1%2Ffile.jpeg?alt=media)增

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FXYXecRW4DuvThZ8SSzUQ%2Ffile.jpeg?alt=media)改

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F7bASNv23GfmBuTVBg1ME%2Ffile.jpeg?alt=media)删

\
**7.copyWithin(target, start = 0, end = this.length)**\
数组实例的copyWithin()方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。

target: 替换目标位置

start:复制起始位置，默认为0，可为负数end:复制结束位置，默认为数组长度，可为负数\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FJINOQMrylSa7YdCX3Fjm%2Ffile.jpeg?alt=media)\
\
看上面第一个例子，起始位置为3，结束位置为5，替换0位，3-5之间的数字是\[4,5]，所以替换掉了\[1,2]第二个例子，起始位置为3，结束位置为4，替换位，3-4之间的数字是\[4]，所以只替换了\[1]第三个例子，起始位-2相当于从右往左数2位，也就是3号位，到-1相当于从右往左数1为，也就是4号位，3-4之间的数字\[4]\


**8.fill(item,start,end)**\
对数组用给定的元素进行填充，数组中已有的元素会被全部抹去\
\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2F3oRPALQA8H9QxLmwdu7S%2Ffile.jpeg?alt=media)\


**9.sort()**\
在原数组的基础上排序，如果调用该方法时没有使用参数，将按字母顺序对数组中的元素进行排序，说得更精确点，是按照字符编码的顺序进行排序。如果想按照其他标准进行排序，就需要提供比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：

* 若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
* 若 a 等于 b，则返回 0。
* 若 a 大于 b，则返回一个大于 0 的值。

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FAT3KOSBabculQ0bMVSFn%2Ffile.jpeg?alt=media)\


**10.reverse()**\
该方法会反转数组项的顺序，不会创建新数组\
\
![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2Fz4GirwIb9qH6nWLLSjWv%2Ffile.jpeg?alt=media)\


### 总结：

不会改变原数组的方法：&#x20;

```javascript
map()
filter()
every()
some()
reduce()
concat()
Array.from(arraylike)
Array.of()
find((item,index,arr) => xxx)
findIndex()
entries()、keys()、values()
includes(item,index)
flat()
flatMap()
slice()
```

会改变原数组的方法：

```javascript
push()
pop()
shift()
unshift()
splice(index,num,item)
copyWithin(target, start = 0, end = this.length)
fill(item,start,end)
sort()
reverse()
```

****

**forEach((item,index,arr) =>{})等一些不会改变原数组的方法，回调中的第三个参数arr其实是原数组对象，所以它们可以在回调中更改arr原数组对象，从而可以达到更改原数组的目的，但是这种写法并不推荐。**

\
\
\
\
