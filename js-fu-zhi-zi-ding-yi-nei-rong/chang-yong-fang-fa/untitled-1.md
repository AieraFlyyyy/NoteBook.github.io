# 字符串方法

**str.replace(searchStr,replaceStr)**\
&#x20;     **searchStr：**查找的字符串\
&#x20;    **replaceStr：**替换的字符串\
&#x20;     ：该方法返回替换后的字符串，不改变原字符串

**str.charAt(index)**\
&#x20;      **index：**返回指定位置的字符\
&#x20;     ：传入两个参数，不会报错，但是第二个参数默认无效，

**str.charCodeAt(index)**\
&#x20;     **index：**返回指定位置的字符的Unicode编码\
&#x20;     ：传入两个参数，不会报错，但是第二个参数默认无效，

**str.concat(a,n)**\
&#x20;     ：传入两个参数，该方法返回拼接后的字符串

**str.match(str)**

![](https://firebasestorage.googleapis.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MLRa6RNxBKuShxAEyKi%2Fuploads%2FLcrwsc97pmZ1ktffXMn1%2Ffile.jpeg?alt=media)

&#x20;     ：在字符串内检索指定的值或找到一个或多个正则表达式的匹配，返回的是值而不是值的位置。\
\
**str.indexOf(str)**\
&#x20;     ：检索字符串，返回的是字符在字符串的下标\


**str.search(str)**\
&#x20;     ：检索字符串，返回的是地址\
&#x20;     首先要明确search()的参数必须是正则表达式或者字符串,而indexOf()的参数只能是字符串。indexOf()是比search()更加底层的方法。\


**str.slice(start,end)**\
&#x20;     **start：**开始位\
&#x20;     **end**：结束位\
&#x20;     ：1.起始位超过字符串的长度，返回空字符串\


**str.split(str)**\
&#x20;     ：把字符串分割为数组，原字符串不变\


**str.toLocaleLowerCase(str)**\
&#x20;     ：把字符串变为小写\


**str.subString(str)**\
&#x20;     ：把字符串变为小写\
