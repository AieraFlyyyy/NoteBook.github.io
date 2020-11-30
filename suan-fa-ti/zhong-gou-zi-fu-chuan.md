# 重构字符串

### 思路： 

1.首先建立一个hash，记录下从a-z每个字符串的出现次数   
2.然后根据大小sort排序，目的是为了把出现次数最多的字符串快速找出来   
3.判断可不可以组成目标字符串，主要是看出现次数最多的字符串的数量是否大于字符串总数量的一半   
4.如果可以组成字符串，则循环拼接

代码：

```javascript
var reorganizeString = function(S) {
    let hashArr = new Array(26).fill(0);
    for(let i=0;i<S.length;i++){
        let item = hashArr[S[i].charCodeAt()-97];
        if(item){
            item.count++;
        }else{
            hashArr[S[i].charCodeAt()-97] = {name:S[i],count:1}
        }
    }
    hashArr = hashArr.filter((v)=>v!=0);
    hashArr.sort((a,b)=>(b.count-a.count));

    let ans = []
    if(hashArr[0].count>Math.ceil(S.length/2)){
        return ""
    }else{
        for(let i=0;i<hashArr[0].count;i++){
            ans.push(hashArr[0].name);
        }
        let cur = 1;
        let i = 1;
        while(cur < hashArr.length){
            ans.splice(i,0,hashArr[cur].name);
            hashArr[cur].count--;
            if(!hashArr[cur].count){
                cur++;
            }
            i+=2;
            if(i>=ans.length+1){
                i=1;
            }

        }
    }
    return ans.join('');
};

```

