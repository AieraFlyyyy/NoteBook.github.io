# JS复制自定义内容

```javascript
var temp = document.createElement('textarea'); 
temp.value = '哈哈哈'; 
document.body.appendChild(temp); 
temp.select();                    // 选择对象 
document.execCommand("Copy");     // 执行浏览器复制命令 
temp.style.display='none';
```

思路： 首先创建一个**textarea或者input**，然后给予他自定义的粘贴内容，接着执行浏览器复制命令，最后把创建的对象**隐藏**

