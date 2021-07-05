---
description: 写这篇是想系统性的总结下webpack相关知识点，我会尽量写的最好
---

# Webpack基础&lt;一&gt;

### 什么是Webpack?

[官网](https://github.com/webpack/webpack)上的webpack定义：

> _webpack is a module bundler. Its main purpose is to bundle JavaScript files for usage in a browser, yet it is also capable of transforming, bundling, or packaging_
>
> #### webpack是模块打包器。它的主要目的是捆绑JavaScript文件以便在浏览器中使用，但它也能够转换、捆绑或打包任何资源或资产。



### 什么是模块打包工具？

比如现在有这几个文件

```javascript
index.js
header.js
content.js
```

index.js中是这么写的

```javascript
var header = require('./header.js')
var content = require('./content.js')

/*
 some code
*/
```

webpack的实际作用其实是把**header**、**content**这样的模块，以及index.js这样的入口文件，打包到一起去，跟index.js文件一起输出成一个js文件



### 什么是Loader

webpack的基础功能只能打包js文件

要想让webpack能够打包css、scss、less、image等资源文件，需要额外对webpack配置loader

> _webpack enables use of_ [_loaders_](https://webpack.js.org/concepts/loaders) _to preprocess files. This allows you to bundle any static resource way beyond JavaScript._
>
> #### webpack允许你使用loaders来预处理文件。这允许您以JavaScript之外的方式绑定任何静态资源。



### 关于Plugin

> _webpack has a rich plugin interface. Most of the features within webpack itself use this plugin interface. This makes webpack **flexible**._
>
> #### webpack有丰富的插件接口。webpack本身的大多数特性都使用这个插件接口。这使得webpack变得灵活。





