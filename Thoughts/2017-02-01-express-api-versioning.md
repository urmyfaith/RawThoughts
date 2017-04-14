# Express.js 中的 API 版本控制

在讲 Express.js 中实现 API 版本控制之前，先来看看为什么要做服务器端的 API 版本控制。

发布出去的 API 就像泼出去的水，不进行版本控制，对某些调用者来说一定会是灾难性的。

举个例子。比如你在一家公司负责后端 API 的开发，同时给公司的 Android 和 iOS 客户端提供接口。
在 1 月份的时候，你开发了一个接口，用于获取文章的详情


```json
@GET /post/:id
//response
{
   "title": "xxx",
   "content": "xxx",
   "author": {
     "id": 123,
     "name": "xxx"
   }
}
```

开发完成后，你很 happy 地告诉客户端同学，获取文章详情就调我这个接口吧，一点问题都没有。客户端同学也很 happy 地调用了。2 月份，客户端开发完成，顺利发布 1.0 版本。这个时候，还没有遇到任何问题。

3 月份的时候，设计变了，文章支持了多作者！所以现在那个相同的 API 返回的结果可能是这样的，

```
@GET /post/:id
// response
{
   "title": "xxx",
   "content": "xxx",
   "author": [
      {
         "id": 123,
         "name": "xxx"
      },
      {
         "id": 456
         "name": "xxx"
      }
   ]
}
```

Boom! 这下可糟了，如果 API 产生了这么重大的变化，那么已经发出去的 1.0 版本的客户端可怎么办啊？（开发过客户端的同学都知道，同一个接口返回这么「脏」的数据是会 crash 的）。这个时候，就需要 API versioning（版本化）了。

## How?

通常来说，API 版本控制有以下几种方式：

### `/api/v1/xxx`

直接把 API 的版本信息放到 URL 里面，[新浪](http://open.weibo.com/wiki/2/users/show)是这么做的。

优点：直观
缺点：URL 被污染了，很不优雅

### `API-VERSION: 3.0`

使用自定义的 header 识别 API 的版本，知乎目前是这么做的。

优点：URL 变得很干净
缺点：不直观，代码不好组织，而且容易产生向后兼容的问题（至少知乎的某些 API 是这样的，一般这种对于 header 的修改都是全局的，某些 API 说好了可以升级到 2.0，但是我调用 2.0 的 `/singin` 都登录不上）

### `Accept: application/vnd.xxx.v2+json`

修改 header 中的 `Accept` 字段，[Github](https://developer.github.com/v3/) 是这么做的。

优缺点同上

最近在用 Node.js 做一个小项目 [DayCache](https://github.com/DayCache/DayCache)，其中就涉及到了给客户端提供 API 调用这一块,当然就也需要 API 的版本控制了。

## `/api/v1/post/:id`

```
// Version 1
@GET /api/v1/post/:id

// Version 2
@GET /api/v2/post/:id
```

对于这种方式的 API 版本控制我们应该怎么实现呢？

首先，把目录结构组织如下，

```
├── api
│   └── v1
│       └── index.js
│       └── signin.js
│       └── signup.js
└── index.js
```

```js
// index.js

...

/*
  // API
  如果需要新增加一个 API 的版本
  1. 把 ./api/v1 目录复制一份，改名为 v2
  2. 在下面的 API_VERSIONS 字典中加入一对 kv，如 `'Version 2': '/v2'`
*/

var API_VERSIONS = {
  'Version 1': '/v1'
}

for (var v in API_VERSIONS) {
  var api = require('./api' + API_VERSIONS[v]);
  api(app);
}

...

```

```js
// api/v1/index.js
// 如果需要加入新的请求路径，只需要在下面加入路径
// 然后在 `api/v1` 下建立同名文件即可
let ROUTE_PATHS = [
  '/signin',
  '/signup',
];

module.exports = function (app) {

  for (let i in ROUTE_PATHS) {
    let path = ROUTE_PATHS[i];
    console.log(path);
    app.use('/api/v1' + path, require('.' + path));
  }

};
```

这样，API 版本化的工作就已经完成了，而且不同版本的 API 拆分地比较干净，同一个版本不同路径的请求也都在不同的文件中。

## `Accept-Version: 1.0`

对于这种请求的 API 版本放到 header 中的，也比较好处理，可以看一下 [express-routes-versioning](https://github.com/Prasanna-sr/express-routes-versioning) 这个中间件（毕竟 Express.js，中间件就是多），它就是比较优雅地处理了这个问题：

```js
// index.js
var app = require('express')();
var routesVersioning = require('express-routes-versioning')();

app.get('/test', routesVersioning({
   "1.0.0": respondV1,
   "~2.2.1": respondV2
}));

// curl -s -H 'accept-version: 1.0.0' localhost:3000/test
// version 1.0.0 or 1.0 or 1 !
function respondV1(req, res, next) {
   res.status(200).send('ok v1');
}

//curl -s -H 'accept-version: 2.2.0' localhost:3000/test
//Anything from 2.2.0 to 2.2.9
function respondV2(req, res, next) {
   res.status(200).send('ok v2');
}
```

把请求 header 中 `Accept-Version` 不同的 API 版本挂在到不同的 handler 中，而且还可以使用类似于 `~2.2.1` 的方式指定版本号。
这个中间件的代码没有多少，可以根据你的需要定制一个，比如 custom header 啦，版本号的匹配规则啦。
