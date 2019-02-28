---
title: 记录学习webpack过程
date: 2018-07-26
updated: 2018-07-26
tags:
  - webpack
categories:
  webpack
---

#### step1:
进入项目根目录
npm init 创建 package.json
npm install --save-dev webpack 
#### step2:
创建app文件夹：用来存放依赖模块js和主模块js
在app中创建Greeter.js  main.js
创建public文件夹：用来存放webpack打包之后用于浏览器引用的文件夹
在public中创建index.html, 并且引入bundle.js  （bundle.js也就是最终打包后的一个集合js的文件）
#### step3:
<!--more-->
正式使用webpack
```
module.exports = {
 entry:///////主模块路径
  output: ////打包之后的配置 {
        path：/////打包之后js文件的路径
         filename：打包之后js文件名
}
}
```
```
module.exports = {
    entry: __dirname + '/app/main.js',   ///__dirname 是当前脚本所在的目录
    output: {
        path: __dirname + '/public',
        filename: 'bundle.js'
    }
}
```

- step1：引入babel 
npm i babel-loader -D   ///在规则中引入loader
npm i babel-core -D   ///babel-loader会引用babel-core的某些api
npm i babel-preset-env -D  //转码规则
- step2：创建.babelrc
```
{
    "presets" : ["env"]   //以babel-preset-env作为转码规则
}
```
- step3：在module.exports中引入
```
    module: {
        rules: [
            {
                test: /\.js$/,   ///以js文件后缀用babel-loader形式加载
                use: [
                    "babel-loader"
                ]
            }
        ]
    },
```