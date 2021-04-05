# Koa

> 学习koa的文档资源：
>
> - https://github.com/demopark/koa-docs-Zh-CN
>
> - https://github.com/koajs/koa
>
> - https://jspang.com/detailed?id=34#toc26
>
> - https://www.npmjs.com/
> - https://www.itying.com/koa

## 1 Koa2快速开始

### 1.1 koa2环境搭建教程

1. 初始化package.json

   ```
   npm init
   ```

2. 安装koa2

   ```npm install koa
   npm install koa
   ```

   ```
   const Koa = require('koa')
   const app = new Koa()
   
   
   app.use( async ( ctx ) => {
     ctx.body = 'hello koa2'
   })
   
   
   app.listen(3000)
   console.log('[demo] start-quick is starting at port 3000')
   ```

3. 启动koa2的demo

   ```
   node index.js
   ```

### 1.3 koa2目类结构分析

**koa2源码核心文件分析**

├── lib
│  ├── application.js
│  ├── context.js
│  ├── request.js
│  └── response.js
└── package.json
这个就是 GitHub https://github.com/koajs/koa上开源的koa2源码的源文件结构，核心代码就是lib目录下的四个文件


**application.js** 是整个koa2 的入口文件，封装了context，request，response，以及最核心的中间件处理流程。
**context.js** 处理应用上下文，里面直接封装部分request.js和response.js的方法
**request.js** 处理http请求

**response.js** 处理http响应

## 2 Koa2路由以及中间件

### 2.1 Koa2路由以及路由get传值

#### 一 、Koa2路由的基本使用

```
npm install --save koa-router
```

```
const Koa = require('koa');
const router = require('koa-router')();  //注意：引入的方式
const app = new Koa();
router.get('/', function (ctx, next) {
ctx.body="Hello koa";
})


router.get('/news,(ctx,next)=>{
ctx.body="新闻page"
});

app.use(router.routes()); //作用：启动路由
app.use(router.allowedMethods()); // 作用： 这是官方文档的推荐用法,我们可以看到router.allowedMethods()用在了路由匹配router.routes()之后,所以在当所有路由中间件最后调用.此时根据ctx.status设置response响应头
app.listen(3000,()=>{
console.log('starting at port 3000');
})
```



#### 二、Koa路由get传值

​	在koa2中GET传值通过request接收，但是接收的方法有两种：query和querystring。

- **query：**返回的是格式化好的参数对象。
  **querystring：**返回的是请求字符串。

```
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();
const router = new Router();
router.get('/', function (ctx, next) {
	ctx.body="Hello koa";
})
router.get('/newscontent,(ctx,next)=>{

	let url =ctx.url;
		//从request中获取GET请求
		let request =ctx.request;
		let req_query = request.query;
		let req_querystring = request.querystring;
		//从上下文中直接获取
		let ctx_query = ctx.query;
		let ctx_querystring = ctx.querystring;
		ctx.body={
		url,
		req_query,
		req_querystring,
		ctx_query,
		ctx_querystring
	}

});
app.use(router.routes()); //作用：启动路由
app.use(router.allowedMethods()); //作用： 当请求出错时的处理逻辑
app.listen(3000,()=>{
	console.log('starting at port 3000');
});
```



#### 三、Koa动态路由

```
//请求方式   http://域名/product/123
router.get('/product/:aid',async (ctx)=>{
   console.log(ctx.params); //{ aid: '123' }  //获取动态路由的数据
   ctx.body='这是商品页面';
})
```



### 2.2 Koa2中间件

#### 一、什么是中间件

- **中间件**：就是匹配路由之前或者匹配路由完成做的一系列的操作，我们就可以把它叫做中间件。
- 在express中间件（Middleware） 是一个函数，它可以访问请求对象（request object (req)）, 响应对象（response object (res)）, 和 web 应用中处理请求-响应循环流程中的中间件，一般被命名为 next 的变量。在Koa中中间件和express有点类似。

- **中间件的功能**：

  执行任何代码。
  修改请求和响应对象。
  终结请求-响应循环。
  调用堆栈中的下一个中间件。

如果我的get、post回调函数中，没有next参数，那么就匹配上第一个路由，就不会往下匹配了。如果想往下匹配的话，那么需要写next()

#### 二、Koa应用的几种中间件

- 应用级中间件

```
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();
const router = new Router();
app.use(async (ctx,next)=>{
	console.log(new Date());
	await next();
})
router.get('/', function (ctx, next) {
	ctx.body="Hello koa";
})
router.get('/news',(ctx,next)=>{
	ctx.body="新闻页面"
});

app.use(router.routes()); //作用：启动路由
app.use(router.allowedMethods()); //作用： 当请求出错时的处理逻辑
app.listen(3000,()=>{
	console.log('starting at port 3000');
});
```



- 路由中间件：
- 错误处理中间件：
- 第三方中间件：

#### 三、 Koa中间件的执行顺序

<img src="https://www.itying.com/koa/data/news/image/20180406/20180406134220_35560.png" alt="img" style="zoom:80%;" />



## 3 请求数据获取

#### 3.1 GET请求数据获取

​	在ko2a中，获取GET请求数据源头是koa中request对象中的query方法或querystring方法，query返回是格式化好的参数对象，querystring返回的是请求字符串，由于ctx对request的API有直接引用的方式，所以获取GET请求数据有两个途径。

1. **是从上下文中直接获取**
      请求对象ctx.query，返回如 { a:1, b:2 }

      请求字符串 ctx.querystring，返回如 a=1&b=2

2. **是从上下文的request对象中获取**

     请求对象ctx.request.query，返回如 { a:1, b:2 }

     请求字符串 ctx.request.querystring，返回如 a=1&b=2

#### 3.2 POST请求数据获取

​	对于POST请求的处理，koa2没有封装获取参数的方法，需要通过解析上下文context中的原生node.js请求对象req，将POST表单数据解析成query string（例如：a=1&b=2&c=3），再将query string 解析成JSON格式（例如：{"a":"1", "b":"2", "c":"3"}）



#### 3.3 koa-bodyparser中间件

​	对于POST请求的处理，koa-bodyparser中间件可以把koa2上下文的formData数据解析到ctx.request.body中

https://www.npmjs.com/package/koa-bodyparser

**1、安装 koa-bodyparser**

```js
npm install --save koa-bodyparser
```

**2、安装** **koa-bodyparser** **引入配置中间件**

```js
var Koa = require('koa');
var bodyParser = require('koa-bodyparser');
var app = new Koa();
app.use(bodyParser());
 
app.use(async ctx => { 
  ctx.body = ctx.request.body;
});
```

**3、通过ctx.request.body 获取post提交的数据**

​	ctx.request.body 获取post提交的数据。middleware的顺序很重要，这个koa-bodyparser必须在router之前被注册到app对象上



## 10 koa其他模块

### 10.1 Koa2后台允许跨域

我们都知道 当域名、端口、协议有任意一个不一样的时候就会存在跨域，那么跨域如何解决呢，有好多种方式：

1、后台允许跨域

2、jsonp

3、websocket 

4、iframe

...等

**koa2后台允许跨域的方法主要有两种**：1.jsonp 2、koa2-cors让后台允许跨域直接就可以在客户端使用ajax请求数据。

**1、安装** **koa2-cors**

```js
npm install --save koa2-cors
```

**2、引入** **koa2-cors 并且配置中间件**

```js
var Koa = require('koa');
var cors = require('koa2-cors');


var app = new Koa();
app.use(cors());
```

koa2-cors官方文档：https://github.com/zadzbw/koa2-cors

### 10.6 koa2-cors

