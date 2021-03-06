# 48.旋转图像

### 题目：

```javascript
给定一个 n × n 的二维矩阵表示一个图像。

将图像顺时针旋转 90 度。

说明：

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

示例 1:

给定 matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

原地旋转输入矩阵，使其变为:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

### 

### 思路：

1. 首页将输入

   1 2 3 

   4 5 6   
   7 8 9

2. 通过交换matrix\[i\]\[j\], matrix\[j\]\[i\] 得到

   1 4 7   
   2 5 8  
    3 6 9

3. 最后将得到每组数组倒序排列即可

   7 4 1   
   8 5 2   
   9 6 3

```javascript
var rotate = function (matrix) {
    let len = matrix.length;
    for (let i = 0; i < len; i++) {
        for (let j = i; j < len; j++) {
            const temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }
    return matrix.map(v => v.reverse())
};
```

