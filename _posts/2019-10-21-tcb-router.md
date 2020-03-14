---
layout: tags
title:  tcb-router 
date:   2019-10-21 23:35:45 +0800
tags: 微信小程序与云开发
---
# tcb-router

[![NPM version](https://camo.githubusercontent.com/a0ac1bbfb9200dc0338f6e299a204feddfe073e4/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f762f7463622d726f757465722e7376673f7374796c653d666c61742d737175617265)](https://npmjs.org/package/tcb-router) [![Build Status](https://camo.githubusercontent.com/02a4672bd18bac46c77f855508eddd36543be95a/68747470733a2f2f7472617669732d63692e6f72672f54656e63656e74436c6f7564426173652f7463622d726f757465722e7376673f6272616e63683d6d6173746572)](https://travis-ci.org/TencentCloudBase/tcb-router) [![npm download](https://camo.githubusercontent.com/3096ddd2ca93178fba9de91edfab16d22f074cfb/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f646d2f7463622d726f757465722e7376673f7374796c653d666c61742d737175617265)](https://npmjs.org/package/tcb-router) [![npm license](https://camo.githubusercontent.com/376426fd7c4caa66f6dd321016302ebd11ded871/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f6c2f7463622d726f757465722e737667)](https://npmjs.org/package/tcb-router) [![Coverage Status](https://camo.githubusercontent.com/1fc11c018c5c787e24a2bd5c5405dd4b09c5357e/68747470733a2f2f636f766572616c6c732e696f2f7265706f732f6769746875622f54656e63656e74436c6f7564426173652f7463622d726f757465722f62616467652e7376673f6272616e63683d6d6173746572)](https://coveralls.io/github/TencentCloudBase/tcb-router?branch=master)

> 基于 koa 风格的小程序·云开发云函数轻量级类路由库，主要用于优化服务端函数处理逻辑

## 云函数端

使用

```js
npm install --save tcb-router
// 云函数的 index.js
const TcbRouter = require('./router');

exports.main = (event, context) => {
    const app = new TcbRouter({ event });
  
    // app.use 表示该中间件会适用于所有的路由
    app.use(async (ctx, next) => {
        ctx.data = {};
        await next(); // 执行下一中间件
    });

    // 路由为数组表示，该中间件适用于 user 和 timer 两个路由
    app.router(['user', 'timer'], async (ctx, next) => {
        ctx.data.company = 'Tencent';
        await next(); // 执行下一中间件
    });

    // 路由为字符串，该中间件只适用于 user 路由
    app.router('user', async (ctx, next) => {
        ctx.data.name = 'heyli';
        await next(); // 执行下一中间件
    }, async (ctx, next) => {
        ctx.data.sex = 'male';
        await next(); // 执行下一中间件
    }, async (ctx) => {
        ctx.data.city = 'Foshan';
        // ctx.body 返回数据到小程序端
        ctx.body = { code: 0, data: ctx.data};
    });

    // 路由为字符串，该中间件只适用于 timer 路由
    app.router('timer', async (ctx, next) => {
        ctx.data.name = 'flytam';
        await next(); // 执行下一中间件
    }, async (ctx, next) => {
        ctx.data.sex = await new Promise(resolve => {
        // 等待500ms，再执行下一中间件
        setTimeout(() => {
            resolve('male');
        }, 500);
        });
        await next(); // 执行下一中间件
    }, async (ctx)=>  {
        ctx.data.city = 'Taishan';

        // ctx.body 返回数据到小程序端
        ctx.body = { code: 0, data: ctx.data };
    });

    return app.serve();

}
```

tips: 小程序云函数的 node 环境默认支持 async/await 语法，推荐涉及到的异步操作时像 demo 中那样使用

## 小程序端

```js
// 调用名为 router 的云函数，路由名为 user
wx.cloud.callFunction({
    // 要调用的云函数名称
    name: "router",
    // 传递给云函数的参数
    data: {
        $url: "user", // 要调用的路由的路径，传入准确路径或者通配符*
        other: "xxx"
    }
});
```

## 接口

### 构造函数

- 参数
  - Object，存入云函数的 `event` 参数
- 示例

```js
const TcbRouter = require('./router');
exports.main = (event, context) => {
    const app = new TcbRouter({ event });
};
```

### app.use

- 参数
  - Function, 应用于所有路由的中间件
- 示例

```js
app.use(async (ctx, next) => {
    ctx.data = {};
    await next(); // 执行下一中间件
});
```

### app.router

- 参数
  - Array|String，路由或路由数组
  - Function, 应用于对应路由的中间伯
- 示例

```js
app.router(['user', 'timer'], async (ctx, next) => {
    ctx.data = {}；
    ctx.data.company = 'Tencent';
    await next(); // 执行下一中间件
});

// 路由为字符串，该中间件只适用于 user 路由
app.router('user', async (ctx, next) => {
    ctx.data.name = 'heyli';
    ctx.body = {code: 0, data: ctx.data}; // 将数据返回给云函数，用ctx.body
});
```

## 测试

```npm
npm run test
```