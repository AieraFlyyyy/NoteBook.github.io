# DL如何入库国服

前前后后也是折腾了有半天时间，现在来复盘一下我用国区steam入库DL的整个过程  
在文章最后给大家准备了一个懒人版流程，不好使你砍我

### 尝试方法一：神秘代码入库法

根据游戏王吧大佬贡献的方法，[原帖在这里](https://tieba.baidu.com/p/5442631985?pn=1)

一、需要的东西   
1.一个梯子，最好是美国的   
2.谷歌浏览器或者同一内核的浏览器   
3.steam账号以及客户端

二、修改商店区域

1.**挂上梯子**，**登陆到网页steam主页**，这时的主页货币单位是RMB

2.随意添加一个游戏到购物车,比如右下角的彩虹六号，点击进入商店页面，再点击添加添加至购物车 页面跳转，在付款的右上角，可以看到一个国家/地区，点击下拉，选择美国

3.这时会弹出一个窗口，询问是否转换，点击位于美国

4.之后页面重新加载，可以看到一行小字，购物车里的游戏已经变成美元

5.注意：不要付款，重新开一个标签，进入steam商店主页，谷歌浏览器直接按F12，进入开发者模式，在弹出的页面里，选中Console

6. 在空白处输入下列代码

```javascript
// Based on Origin version by SteamDb.info
(function() {
if (location.hostname !== 'store.steampowered.com') {
alert('Run this code on the Steam Store!');
return;
} else if (typeof jQuery !== 'function') {
ShowAlertDialog('Fail', 'This page has no jQuery, try homepage.');
return;
} else if (document.getElementById('header_notification_area') === null) {
ShowAlertDialog('Fail', 'You have to be logged in.');
return;
}

var freePackages = [158055];

var loaded = 0,
total = freePackages.length,
modal = ShowBlockingWaitDialog('Executing...', 'Please wait until all requests finish.');

for (var i = 0; i < total; i++) {
jQuery.post(
'//store.steampowered.com/checkout/addfreelicense', {
action: 'add_to_cart',
sessionid: g_sessionID,
subid: freePackages[i]
},
function(data) {
loaded++;

modal.Dismiss();

if (loaded === total) {
ShowAlertDialog('All done!', 'Enjoy.');
} else {
modal = ShowBlockingWaitDialog('Executing...', 'Loaded ' + loaded + '/' + total);
}
}
).fail(function() {
loaded++;

modal.Dismiss();

if (loaded === total) {
ShowAlertDialog('All done!', 'Enjoy.');
} else {
modal = ShowBlockingWaitDialog('Executing...', 'Loaded ' + loaded + '/' + total);
}
});
}
}());
```

然后按回车，商店页面一会儿会弹出一个小窗口，关掉就好   
然后进入账户明细看一下是不是添加成功

![](../.gitbook/assets/image%20%2824%29.png)

7.如果有这行字，那么在浏览器地址栏输入steam://install/601510   
按回车就可以了，游戏开始下载   
完成

：这里我至少耽误了一个小时，反复横跳，左右摇摆，但是仍然不能如上图一样，把游戏添加到我的账户明细里，因为耽误太长时间且没有头绪，我直接放弃这种方法。

（文章末尾我会说明我操作失误的点，大家只要按照我说的去做，这个方法应该也能成功，上面贴子里有在我写文章的前几天都能入库成功的楼）  


### 尝试方法二：官网拉起法

这个方法更为简单易懂，由游戏王决斗链接吧的大佬贡献，[原帖在这里](https://tieba.baidu.com/p/6396192971?pid=130033011275&cid=0#128861010941l)

1.首先第一步 **打开梯子**

2.第二步 **登录你电脑的steam**

3. 浏览器打开[http://store.steampowered.com/app/601510/](http://jump2.bdimg.com/safecheck/index?url=x+Z5mMbGPAvCo7g/EAR3RubtNIebTeUla7hjas4nPgKMdRnyGra67oTpKPFXA4co1/RplAsgmss3mT2zNgrbik3rzpgEjo9p3NSPGoZm0irm++TUoYlIs40J7mqSkO/CiEp0wVGGuKuayOAsHhfstAyVZx73NvBLdj2oeHoEzTI=) 也就是决斗链接的商店页面，**不要在浏览器登陆你的steam账号。**

4.然后点击下面的 playgame 开启游戏 steam会跳出来问你安装在哪里 选择完路径 游戏就入库成功了



：这里我一开始也是失败了，在第4步点击playgame拉起游戏时，steam转跳到了游戏页面，接着就提示**该地区不提供本产品**之类的。

：我也试过把游戏加入心愿单，然后点击play，之后就弹出一个文件准备中的框，然后，然后就没有然后了，直接无事发生

那么怎么样才能添加成功呢？

其实，很简单，朋友们

![](../.gitbook/assets/image%20%2825%29.png)

我们之所以失败，其实是因为没有**提前打开全局梯子！！！！**

真相就是这么赤果果。



### 说好的懒人版：

**1.一定要先打开梯子！！开全局！！**  
  
**2.保证梯子开了全局！！再打开steam客户端！！**

3. 浏览器打开[http://store.steampowered.com/app/601510/](http://jump2.bdimg.com/safecheck/index?url=x+Z5mMbGPAvCo7g/EAR3RubtNIebTeUla7hjas4nPgKMdRnyGra67oTpKPFXA4co1/RplAsgmss3mT2zNgrbik3rzpgEjo9p3NSPGoZm0irm++TUoYlIs40J7mqSkO/CiEp0wVGGuKuayOAsHhfstAyVZx73NvBLdj2oeHoEzTI=) 也就是决斗链接的商店页面，**不要在浏览器登陆你的steam账号。**

4.然后**点击网页上**的 playgame/开始游戏，**steam客户端**会跳出来问你安装在哪里 选择完路径 游戏就入库成功了！



————————————————————————————————————————————————————————————

### 插播一条紧急新闻！！

好家伙，刚刚我好不容易下好了全部的数据，一阵白屏，直接给我回到解放前，重试了两遍也是如此，最后百度了才知道，以我血泪的教训告诉大家！！

**steam端下载数据的时候，千万不要手贱登陆手机端玩！！！玩一会中途退出也不行！！！**



