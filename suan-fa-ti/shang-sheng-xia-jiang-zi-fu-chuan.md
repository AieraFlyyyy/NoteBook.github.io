# 上升下降字符串

### 题目

```javascript
给你一个字符串 s ，请你根据下面的算法重新构造字符串：

从 s 中选出 最小 的字符，将它 接在 结果字符串的后面。
从 s 剩余字符中选出 最小 的字符，且该字符比上一个添加的字符大，将它 接在 结果字符串后面。
重复步骤 2 ，直到你没法从 s 中选择字符。
从 s 中选出 最大 的字符，将它 接在 结果字符串的后面。
从 s 剩余字符中选出 最大 的字符，且该字符比上一个添加的字符小，将它 接在 结果字符串后面。
重复步骤 5 ，直到你没法从 s 中选择字符。
重复步骤 1 到 6 ，直到 s 中所有字符都已经被选过。
在任何一步中，如果最小或者最大字符不止一个 ，你可以选择其中任意一个，并将其添加到结果字符串。

请你返回将 s 中字符重新排序后的 结果字符串 。
```

**解释题目：**  
1.循环一次，把**最小**的字符串挑选出来放到新字符串中，要求是**后一个字符串**要比前一个字符串**大**：

```javascript
s = 'aaaaccccffffgggg';
// 遍历第一遍
res = 'acfg';
```

2.循环一次，把**最大**的字符串挑选出来放到新字符串中，要求是后一个字符串要比前一个字符串**小**：

```javascript
// 遍历第二遍
res = 'acfggfca';
```

3.重复1、2步骤，直到所有字符串都遍历完成



### 解法

1.建一个hash对象，使用hash记录每个字符串出现次数，长度为26（26个字母长度）  
2.接着遍历整个hash对象，根据 a-z  --&gt;  hash\[0-25\]  --&gt;  hash\[\(97-122\) - 97\]的对应顺序，设置字符串出现次数 --&gt; hash\[s.charCodeAt\(i\) - 97\]++  
3.处理之后，i越小hash\[i\]越小，所以从**最小值**挑选则let i = 0; i++ ，从最大值挑选则let i = 25; i--   
  
过程说明：

```javascript
s = 'abcabcabcdddd';
------>
[3, 3, 3, 4, 0, 0, 0, 0, 0 ....] abcd
 a  b  c  d  e  f  g  h  i ....  
                                 <------    
[2, 2, 2, 3, 0, 0, 0, 0, 0 ....] abcddcba
 a  b  c  d  e  f  g  h  i ....
 
------>
[1, 1, 1, 2, 0, 0, 0, 0, 0 ....] abcddcbaabcd
 a  b  c  d  e  f  g  h  i ....
                                 <------
[0, 0, 0, 1, 0, 0, 0, 0, 0 ....] abcddcbaabcdd
 a  b  c  d  e  f  g  h  i ....
```

```javascript
var sortString = function (s) {
    const hash = new Array(26).fill(0);
    for (let i = 0; i < s.length; i++) {
        hash[s.charCodeAt(i) - 97]++;
    }
    const res = [];
    let rest = s.length;
    while (rest > 0) {
        for (let i = 0; i < 26; i++) {
            if (hash[i] > 0) {
                res.push(String.fromCharCode(i + 97));
                hash[i]--;
                rest--;
            }
        }
        for (let i = 25; i >= 0; i--) {
            if (hash[i] > 0) {
                res.push(String.fromCharCode(i + 97));
                hash[i]--;
                rest--;
            }
        }
    }
    return res.join('');
};
```

