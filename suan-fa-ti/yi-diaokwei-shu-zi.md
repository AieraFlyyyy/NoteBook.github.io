# 移掉K位数字

记录一道算法题：

```javascript
给定一个以字符串表示的非负整数 num，移除这个数中的 k 位数字，使得剩下的数字最小。

注意:

num 的长度小于 10002 且 ≥ k。
num 不会包含任何前导零。
示例 1 :

输入: num = "1432219", k = 3
输出: "1219"
解释: 移除掉三个数字 4, 3, 和 2 形成一个新的最小的数字 1219。
示例 2 :

输入: num = "10200", k = 1
输出: "200"
解释: 移掉首位的 1 剩下的数字为 200. 注意输出不能有任何前导零。
示例 3 :

输入: num = "10", k = 2
输出: "0"
解释: 从原数字移除所有的数字，剩余为空就是0。
```

 思路：

1.找最小的数，高位数必不能是大的  
2.从左到右遍历，比较当前数和它左边的数，我们使用栈结构来操作  
3.假设有 `123456` 这样的增序数，每个数字都大于它左边的数，那么我们对于这种数字只需要删除它的后k位尾数。

以下是实际出入栈例子：

```javascript
"1432219"   3
bottom[1        ]top   
bottom[1 4      ]top          bottom[1 3      ]top	4出栈
bottom[1 2      ]top	3出栈
bottom[1 2 2    ]top   
bottom[1 2 1    ]top	2出栈 1入栈 出栈的个数满3个了，停止出栈
bottom[1 2 1 9  ]top	9入栈
```

完整代码：

```javascript
var removeKdigits = function (num, k) {
  const stack = [];
  for (let i = 0; i < num.length; i++) {
    // c代表当前遍历数字
    const c = num[i];
    // 判断stack尾部数据是否比当前数字大，是则尾数出栈，k--
    while (k > 0 && stack.length && stack[stack.length - 1] > c) {
      stack.pop();
      k--;
    }
    // 如果当前数字不是0，或者stach长度不为0，则入栈
    if (c != '0' || stack.length != 0) {
      stack.push(c);
    }
  }
  // 如果上面的逻辑走完k还大于0，则直接把stach尾部k位数据出栈
  while (k > 0) {
    stack.pop();
    k--;
  }
  return stack.length == 0 ? "0" : stack.join('');
};
```

