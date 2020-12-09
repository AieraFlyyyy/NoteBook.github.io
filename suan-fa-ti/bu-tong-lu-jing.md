# 不同路径

### 题目:

![](../.gitbook/assets/image%20%2814%29.png)

```javascript
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？


例如，上图是一个7 x 3 的网格。有多少可能的路径？

示例 1:

输入: m = 3, n = 2
输出: 3

解释:
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向右 -> 向下
2. 向右 -> 向下 -> 向右
3. 向下 -> 向右 -> 向右

示例 2:

输入: m = 7, n = 3
输出: 28
```

### 思路:

这道题其实就是杨辉三角

| 1 | 1 | 1 |
| :---: | :---: | :---: |
| 1 | 2 | 3 |
| 1 | 3 | 6 |

| 1 | 1 | 1 | 1 | 1 |
| :---: | :---: | :---: | :---: | :---: |
| 1 | 2 | 3 | 4 | 5 |
| 1 | 3 | 6 | 10 | 15 |

根据上面两个表格可以看到，每个点的最大路径数量其实就是该点的上节点（A\[m\]\[n-1\]）和左节点（A\[m-1\]\[n\]）的数量相加。

所以我们可以构建一个二维数组来表示这个表格：

```javascript
var uniquePaths = function (m, n) {
    var res = new Array(m);
    for (let i = 0; i < m; i++) {
        res[i] = new Array(n);
        res[i][0] = 1;
    }
    for (let j = 0; j < n; j++) {
        res[0][j] = 1;        
    }
    
    for (let i = 1; i < m; i++) {
        for (let j = 1; j < n; j++) {
            res[i][j] = res[i-1][j] + res[i][j-1];
        }
    }
    return res[m-1][n-1];
};
```



接下来介绍一种优化思路  
看下图的演变规律，res\[j\] = res\[j\] + \[resj-1\] ，我们可以直接用一个数组来算出最终的数

![](../.gitbook/assets/image%20%2813%29.png)

可以得到更优化的代码：

```javascript
var uniquePaths = function (m, n) {
    var res = new Array(n).fill(1);
    for (let i = 1; i < m; i++) {
        for (let j = 1; j < n; j++) {
            res[j] = res[j] + res[j-1];
        }
    }
    return res[n-1]
};
```

