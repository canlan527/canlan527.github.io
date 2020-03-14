---
layout: tags
title:  TypeScript项目配置
date:   2020-02-26 19:13:43 +0800
tags: TypeScript
---
# TypeScript

## 基础环境搭建

使用IDE是 VSCode，我们电脑需要安装node，IDE再装几个插件，能够很好的提示我们错误问题。

### VScode插件---Pritter

### 对vscode进行设置 

- 选中save 
- 引号设置单引号

### 安装typescript

```
npm install typescript@3.6.4 -g
```

### 安装ts-node

因为node不能直接识别ts文件，所以我们需要运行

```js
tsc demo.ts
//然后将编译好的js用node运行
node demo.js
```

如果我们使用ts-node的话 可以一次到位 编译ts文件

```js
npm install ts-node 

ts-node demo.ts // 直接一步编译
```



## 在webpack的环境下使用ts开发

安装webpack环境：
在前面的基础上(tsconfig.json)：
1、npm init 初始化package.json
2、tsc --init 初始化tsconfig.json
3、npm i webpack webpack-cli webpack-dev-server -D
4、build文件夹  存放配置文件 直接拷贝过来  四个文件
5、npm i ts-loader typescript -D
6、webpack.base.config  html-webpack-plugin 生成首页，并把js文件关联到html中，
所以需要安装 npm i html-webpack-plugin –D 在src中创建index.html
7、webpack.dev.config cheap-module-eval-source-map 增强编译调试 
8、npm i clean-webpack-plugin -D 自动清空dist目录
9、webpack.config.js    npm i webpack-merge -D  把配置文件合并
10、修改package.json的main，修改入口文件: "main": "./src/index.ts",
11、修改package.json的scripts:   "start": "webpack-dev-server --mode=development --config ./build/webpack.config.js",
12、npm start 启动   index.ts中写代码，看界面的控制台
13、配置生产环境，修改package.json的scripts，
命令：  "build": "webpack --mode=production --config ./build/webpack.config.js",
能够生成dist文件夹