# 识别图片文字

最近朋友找到我，问能不能根据做一个根据题库自动得出答案的app出来，他提供题库和答案

啧，小case，先来个思维导图

![](../.gitbook/assets/4421624500938_.pic.jpg)

本着先易后难的态度（实际上是懒）直接排除第一种方案

接着思考下第二种方案的可行性

![](../.gitbook/assets/image%20%2855%29.png)

#### 获取拍照权限好实现，一个input就搞定

```javascript
<input type='file' accept='image/*' capture='camera'/>
```

但如何识别图片中的文字内容

先问问google

![](../.gitbook/assets/image%20%2856%29.png)

根据关键字，我发现了一个可以实现图片文字识别功能的js库 - Tesseract.js

![](../.gitbook/assets/demo.gif)

看下效果，好像还行，那么我们先写个Demo吧

```javascript
import React from 'react';
import Tesseract from 'tesseract.js';

export default class APP extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            message: '',
            result: '',
            loading: false,
        }
    }

    handleInputChange = (e) => {
        const file = e.target.files && e.target.files[0];
        this.setState({ result: '' });
        this.handleImageToTxt(file);
    }

    handleImageToTxt = (file) => {
        Tesseract.recognize(
            // 支持传文件/文件路径/base64编码等格式
            file,
            // 简体中文
            'chi_sim',
            { logger: m => this.setState({ loading: true, message: m.status }) }
        ).then(({ data: { text } }) => {
            const result = text.split(' ').join('');
            this.setState({ loading: false, message: '', result })
        })
    }

    render() {
        const { message, loading, result } = this.state;
        return <div style={{ fontSize: '80px' }}>
            <a>点击下方按钮拍照</a>
            <input
                style={{ fontSize: '50px' }}
                onChange={this.handleInputChange}
                type='file' accept='image/*' capture='camera'
            />
            {
                loading &&
                <div>
                    <span>加载中...</span>
                    <span>{message}</span>
                </div>
            }
            {
                result &&
                <div>
                    <span>结果：{result}</span>
                </div>
            }
        </div>
    }
}
```

![](../.gitbook/assets/image%20%2863%29.png)

所有支持语言在[这里](https://tesseract-ocr.github.io/tessdoc/Data-Files#data-files-for-version-400-november-29-2016)查看

![](../.gitbook/assets/image%20%2859%29.png)

![](../.gitbook/assets/image%20%2830%29.png)

效果好像还不错哦

![](../.gitbook/assets/image%20%2832%29.png)

加大难度试试

![](../.gitbook/assets/wechatb5103154f290555d92ff02ae2fcefa03.png)

![](../.gitbook/assets/image%20%2857%29.png)

继续！

![](../.gitbook/assets/wechat224e6d36bd9449f12f1af88b510acab9.png)

![](../.gitbook/assets/image%20%2864%29.png)

哈哈哈，看来是有点为难它了，但是基础功能我们还是成功实现了，鼓掌鼓掌

下一步就是开发一个全能识文app，接着升职加薪，赢取白富美，走上人生巅峰（醒醒，工头喊你起来搬砖啦🧱）



总之呢，由于识别率实在太低，本app宣布腹死胎中

要想提高识别率也有办法，Tesseract.js允许你自己训练文字库 - [操作导航](https://github.com/naptha/tesseract.js/blob/master/docs/faq.md)

等我朋友什么时候挣到钱来投我这个项目，本项目再继续启动吧

