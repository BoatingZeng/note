# css

## 基本选择器
1. #id id选择器
2. .class 类选择器
3. [attribute] 属性选择器
4. :link 伪类或伪元素

## 组合
1. 两个选择器间没有空格，表示交集
2. 空格，表示后代(包括后代的后代)，多次空格，则是指定后代的后代
3. >，表示直接后代，多个>，则是直接后代的直接后代
4. +，表示同辈的后一个元素。p+p:与前一个p同辈的后一个p。
5. ~，选择同辈的后面的所有元素。p~p，与前p同辈的后面所有p。

# Vue

## Vue admin 里用到的模块

* axios：用来发http请求，Promise形式
* vue-axios：封装axios，应该跟vue-resource差不多，vue-resource好像有点问题，所以我也换了
* vue-nprogress：用来做进度条
* vue-auth：权限管理，限制路由，使用jwt，并且会检测http请求和响应，获取jwt或者写入token到header
* vuex-router-sync：统一管理路由状态

# WebPack

## 问题
* UglifyJs好像不支持es6，用typescript的es6选项时，uglify会出错。vue-cli默认就开了babel-loader。
* error TS2686: 'XXX' refers to a UMD global, but the current file is a module. Consider adding an import instead。想把THREE作为全局使用，但是在编译时会出现这样的问题。***暂时设置ts-loader的happyPackMode来解决***
* sass模块的引用，当使用alias时，要在设定的alias前面加~。也就是说用alias时，要以~开头。

## 模块的引用

模块A，如果几个模块(B、C、D)同时引用模块A，它们所用的其实是同一个模块A。模块A里的局部变量(var 声明的)，B、C、D里访问的A的局部变量，是指向相同的变量，而不是复制。所以在不同模块里操作A，对A的影响是跨模块的。这个规则在nodejs里也是一样的。如果删除了require.cache，则会是新的模块。**所以在模块里的用局部变量时要小心。**
