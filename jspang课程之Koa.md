# jspang课程之Koa

## 1 Koa开发环境搭建

### 1.1 安装Node.js



### 1.2 搭建环境

```bash
cd code
mkdir koa2
cd koa2
```

进入之后，初始化生产package.json文件

```bash
npm init -y
```

安装koa包，这里使用npm来进行安装

```bash
npm install --save koa
```

### 1.3 HelloKoa2

```javascript
const Koa = require('koa')
const app = new Koa()

app.use( async ( ctx ) => {
  ctx.body = 'hello koa2'
})

app.listen(3000)
console.log('[demo] start-quick is starting at port 3000')
```

在命令行中输入:

```bash
node index.js
```

然后在浏览器中输入：[http://127.0.0.1:3000](http://127.0.0.1:3000/) 就可以看到结果了。

## 2 Get请求的接收

### 2.1 query和querystring区别

在koa2中GET请求通过request接收，但是接受的方法有两种：query和querystring。

- query：返回的是格式化好的参数对象。
- querystring：返回的是请求字符串。

```javascript
const Koa = require('koa');
const app = new Koa();
app.use(async(ctx)=>{
    let url =ctx.url;
    let request =ctx.request;
    let req_query = request.query;
    let req_querystring = request.querystring;

    ctx.body={
        url,
        req_query,
        req_querystring
    }

});

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
});
```

启动一切正常可在浏览器中使用[http://127.0.0.1:3000?user=jspang&age=18来进行访问。我们在网页中可以得到一串JSON字符串，这是不是很想后端传给我们的接口。](http://127.0.0.1:3000/?user=jspang&age=18来进行访问。我们在网页中可以得到一串JSON字符串，这是不是很想后端传给我们的接口。)

```json
{"url":"/?user=jspang&age=18","req_query":{"user":"jspang","age":"18"},"req_querystring":"user=jspang&age=18"}
```

### 2.2 直接从ctx中获取Get请求

- 除了在ctx.request中获取Get请求外，还可以直接在ctx中得到GET请求。ctx中也分为query和querystring。有了上边的联系，我们对它们的区别已经很熟悉，不再累述。接着上边的代码，我们看一下在ctx下直接获取GET请求的方式。

```js
const Koa = require('koa');
const app = new Koa();
app.use(async(ctx)=>{
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

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
});
```

总结：获得GET请求的方式有两种，一种是从request中获得，一种是一直从上下文中获得。获得的格式也有两种：query和querystring。大家要区分记忆，并根据实际需求进行灵活变换。



## 3 Post请求的接收

对于POST请求的处理，Koa2没有封装方便的获取参数的方法，需要通过解析上下文context中的原生node.js请求对象req来获取。

### 3.1 获取Post请求的步骤

1. 解析上下文ctx中的原生node.js对象的req。
2. 将POST表单数据解析成query string-字符串
3. 将字符串转换成JSON格式

### 3.2 ctx.request和ctx.req的区别

- ctx.request:是Koa2中context经过封装的请求对象，它用起来更直观和简单。
- ctx.req:是context提供的node.js原生HTTP请求对象。这个虽然不那么直观，但是可以得到更多的内容，适合我们深度编程。

### 3. 3 ctx.method得到的请求类型

Koa2中提供了ctx.method属性，可以轻松的得到请求的类型，然后根据请求类型编写不同的相应方法，这在工作中非常常用。

```javascript
const Koa = require('koa');
const app = new Koa();
app.use(async(ctx)=>{
    //当请求时GET请求时，显示表单让用户填写
    if(ctx.url==='/' && ctx.method === 'GET'){
        let html =`
            <h1>Koa2 request post demo</h1>
            <form method="POST"  action="/">
                <p>userName</p>
                <input name="userName" /> <br/>
                <p>age</p>
                <input name="age" /> <br/>
                <p>webSite</p>
                <input name='webSite' /><br/>
                <button type="submit">submit</button>
            </form>
        `;
        ctx.body =html;
    //当请求时POST请求时
    }else if(ctx.url==='/' && ctx.method === 'POST'){
        ctx.body='接收到请求';
    }else{
        //其它请求显示404页面
        ctx.body='<h1>404!</h1>';
    }
})

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
})
```



### 3.4 解析Node原生POST参数



### 3.5 POST字符串解析JSON对象





## 4 koa-bodyparser中间件

已经学会如何自己编写代码接收并解析POST请求，这样最基本的功能其实是不用我们自己写的，一定有造好的轮子让我们使用，koa-bodyparser就是一个造好的轮子。我们在koa中把这种轮子就叫做中间件。对于POST请求的处理，koa-bodyparser中间件可以把koa2上下文的formData数据解析到ctx.request.body中。

- 安装中间件

  ```bash
  npm install --save koa-bodyparser@3
  ```

- 引入使用

  ```bash
  const bodyParser = require('koa-bodyparser');
  ```

  ```bash
  app.use(bodyParser());
  ```

在代码中使用后，直接可以用ctx.request.body进行获取POST请求参数，中间件自动给我们作了解析。

```javascript
const Koa  = require('koa');
const app = new Koa();
const bodyParser = require('koa-bodyparser');

app.use(bodyParser());

app.use(async(ctx)=>{
    if(ctx.url==='/' && ctx.method==='GET'){
        //显示表单页面
        let html=`
            <h1>JSPang Koa2 request POST</h1>
            <form method="POST" action="/">
                <p>userName</p>
                <input name="userName" /><br/>
                <p>age</p>
                <input name="age" /><br/>
                <p>website</p>
                <input name="webSite" /><br/>
                <button type="submit">submit</button>
            </form>
        `;
        ctx.body=html;
    }else if(ctx.url==='/' && ctx.method==='POST'){
         let postData= ctx.request.body;
         ctx.body=postData;
    }else{
        ctx.body='<h1>404!</h1>';
    }

});


app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
});
```



## 5 Koa2原生路由实现

如果你已经在前端 工作岗位奋战了几个月，你会清楚的知道路由对于编程的重要性，它就是前端的业务逻辑和思想。我们通过判断进行各种页面的展示效果。这节课在不借助中间件的情况下，我们用原生方法简单实现以下路由。

```javascript
const Koa = require('koa')
const app = new Koa()

app.use( async ( ctx ) => {
  let url = ctx.request.url
  ctx.body = url
})
app.listen(3000)
```

### 5.1 原生路由的实现

原生路由的实现需要引入fs模块来读取文件。然后再根据路由的路径去读取，最后返回给页面，进行渲染。

```javascript
const Koa = require('koa');
const fs = require('fs');
const app = new Koa();

function render(page){
    
    return new Promise((resolve,reject)=>{
        let pageUrl = `./page/${page}`;
        fs.readFile(pageUrl,"binary",(err,data)=>{
            console.log(444);
            if(err){
                reject(err)
            }else{
                resolve(data)
            }
        })
    })
}

async function route(url){
	let page = '404.html'
    switch(url){
        case '/':
            page = 'index.html'
            break;
        case '/index':
            page = 'index.html'
            break;
        case '/todo':
            page = 'todo.html'
            break;
        case '/404':
            page = '404.html'
            break;
        default:
            break;
	}
    let html = await render(page)
	return html;
}
app.use(async(ctx)=>{
    let url = ctx.request.url;
    let html = await route(url);

    ctx.body=html;
})
app.listen(3000);
console.log('starting at 3000');

```



## 6 Koa-router中间件

### 6.1 入门

- 安装koa-router中间件

  ```bash
  npm install --save koa-router
  ```

- koa-router基础案例

  ```javascript
  const Koa = require('koa');
  const Router = require('koa-router');
  
  const app = new Koa();
  const router = new Router();
  
  router.get('/', function (ctx, next) {
      ctx.body="Hello JSPang";
  });
  
  app
  	.use(router.routes())
  	.use(router.allowedMethods());
  app.listen(3000,()=>{
      console.log('starting at port 3000');
  })
  ```

- 多页面配置

  ```javascript
  const Koa = require('koa');
  const Router = require('koa-router');
  
  const app = new Koa();
  const router = new Router();
  
  router.get('/', function (ctx, next) {
      ctx.body="Hello JSPang";
  })
  .get('/todo',(ctx,next)=>{
      ctx.body="Todo page"
  });
  
  app
    .use(router.routes())
    .use(router.allowedMethods());
    app.listen(3000,()=>{
        console.log('starting at port 3000');
    });
  ```

  

### 6.2 层级

有时候我们想把所有的路径前面都再加入一个级别，比如原来我们访问的路径是http://127.0.0.1:3000/todo，现在我们希望在所有的路径前面都加上一个jspang层级，把路径变成http://127.0.0.1:3000/jspang/todo.这时候就可以使用层级来完成这个功能。路由在创建的时候是可以指定一个前缀的，这个前缀会被至于路由的最顶层，也就是说，这个路由的所有请求都是相对于这个前缀的。

```python
const router = new Router({
      prefix:'/jspang'
})
```

#### 路由层级

设置前缀一般都是全局的，并不能实现路由的层级，如果你想为单个页面设置层级，也是很简单的。只要在use时使用路径就可以了。

例如这种写法装载路由层级，这里的router相当于父级：router.use(‘/page’, page.routes(), page.allowedMethods())。

通过这种写法的好处是并不是全局的，我们可以给不同的路由加层级。

代码如下：我们声明了两个路由，第一个是home,第二个是page.然后通过use赋予不同的前层级。

```javascript
const Koa = require('koa');
const app = new Koa();
const Router = require('koa-router');


let home = new Router();
home.get('/jspang',async(ctx)=>{
    ctx.body="Home JSPang";
}).get('/todo',async(ctx)=>{
    ctx.body ='Home ToDo';
})




let page = new Router();
page.get('/jspang',async(ctx)=>{
    ctx.body="Page JSPang";
}).get('/todo',async(ctx)=>{
    ctx.body ='Page ToDo';
})

//装载所有子路由
let router = new Router();
router.use('/home',home.routes(),home.allowedMethods());
router.use('/page',page.routes(),page.allowedMethods());


//加载路由中间件
app.use(router.routes()).use(router.allowedMethods());




app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
});
```

### 6.3 参数

```javascript
const Koa = require('koa');
const Router = require('koa-router');
const app = new Koa();
const router = new Router();
router.get('/', function (ctx, next) {
    ctx.body=ctx.query;
});
app
  .use(router.routes())
  .use(router.allowedMethods());
  app.listen(3000,()=>{
      console.log('starting at port 3000');
  });
```

## 7 Koa2中使用cookie

开发中制作登录和保存用户信息在本地，最常用的就是cookie操作。比如我们在作一个登录功能时，希望用户在接下来的一周内都不需要重新登录就可以访问资源，这时候就需要我们操作cookie来完成我们的需求。koa的上下文（ctx）直接提供了读取和写入的方法。

### 7.1 写入Cookie操作：

```javascript
const Koa  = require('koa');
const app = new Koa();

app.use(async(ctx)=>{
    if(ctx.url=== '/index'){
        ctx.cookies.set(
            'MyName','JSPang'
        );
        ctx.body = 'cookie is ok';
    }else{
        ctx.body = 'hello world'
    }
});

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
})
```

写好后预览，打开F12可以在Application中找到Cookies选项。就可以找到我们写入的name和value了。

### 7.2 Cookie选项

比如我们要存储用户名，保留用户登录状态时，你可以选择7天内不用登录，也可以选择30天内不用登录。这就需要在写入是配置一些选项：

- domain：写入cookie所在的域名
- path：写入cookie所在的路径
- maxAge：Cookie最大有效时长
- expires：cookie失效时间
- httpOnly:是否只用http请求中获得
- overwirte：是否允许重写

```javascript
const Koa  = require('koa');
const app = new Koa();

app.use(async(ctx)=>{
    if(ctx.url=== '/index'){
        ctx.cookies.set(
            'MyName','JSPang',{
                domain:'127.0.0.1', // 写cookie所在的域名
                path:'/index',       // 写cookie所在的路径
                maxAge:1000*60*60*24,   // cookie有效时长
                expires:new Date('2018-12-31'), // cookie失效时间
                httpOnly:false,  // 是否只用于http请求中获取
                overwrite:false  // 是否允许重写
            }
        );
        ctx.body = 'cookie is ok';
    }else{
        ctx.body = 'hello world'
    }
});

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
})
```

### 7.3 读取Cookie

```javascript
const Koa  = require('koa');
const app = new Koa();

app.use(async(ctx)=>{
    if(ctx.url=== '/index'){
        ctx.cookies.set(
            'MyName','JSPang',{
                domain:'127.0.0.1', // 写cookie所在的域名
                path:'/index',       // 写cookie所在的路径
                maxAge:1000*60*60*24,   // cookie有效时长
                expires:new Date('2018-12-31'), // cookie失效时间
                httpOnly:false,  // 是否只用于http请求中获取
                overwrite:false  // 是否允许重写
            }
        );
        ctx.body = 'cookie is ok';
    }else{
        if( ctx.cookies.get('MyName')){
            ctx.body = ctx.cookies.get('MyName');
        }else{
            ctx.body = 'Cookie is none';
        }

    }
});

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
})
```

## 8 Koa2的模板初始(ejs)

开发中不可能把所有的html代码全部卸载JS里，这显然不现实，也没办法完成大型web开发。必须借用模板机制来帮助我们开发，这节课我们就简单了解一下Koa2的模板机制，koa2的模板机制要依靠中间件来完成开发。

- 安装中间件

  ```bash
  cnpm install --save koa-views
  ```

  需要注意的是是koa-views 而不是koa-view。

- 安装ejs模板引擎

  ejs是个著名并强大的模板引擎，可以单独安装。很多开源软件都采用了ejs模板引擎。

  ```bash
  npm install --save ejs
  ```

- 编写模板

  安装好ejs模板引擎后，就可以编写模板了，为了模板统一管理，我们新建一个view的文件夹，并在它下面新建index.ejs文件。

  views/index.ejs

- 编写koa文件

  ```javascript
  const Koa = require('koa')
  const views = require('koa-views')
  const path = require('path')
  const app = new Koa()
  
  // 加载模板引擎
  app.use(views(path.join(__dirname, './view'), {
    extension: 'ejs'
  }))
  
  app.use( async ( ctx ) => {
    let title = 'hello koa2'
    await ctx.render('index', {
      title
    })
  })
  
  app.listen(3000,()=>{
      console.log('[demo] server is starting at port 3000');
  })
  ```

  

## 9 koa-static静态资源中间件

  在后台开发中不仅有需要代码处理的业务逻辑请求，也会有很多的静态资源请求。比如请求js，css，jpg，png这些静态资源请求。也非常的多，有些时候还会访问静态资源路径。用koa2自己些这些静态资源访问是完全可以的，但是代码会雍长一些。所以这节课我们利用koa-static中间件来实现静态资源的访问。

- 安装koa-static

  ```bash
  npm install --save koa-static
  ```

- 新建static文件夹：在static文件中放入图片，css和js文件。

    ```javascript
  const Koa = require('koa')
  const path = require('path')
  const static = require('koa-static')
  
  const app = new Koa()
  
  
  const staticPath = './static'
  
  app.use(static(
    path.join( __dirname,  staticPath)
  ))
  
  
  app.use( async ( ctx ) => {
    ctx.body = 'hello world'
  })
  
  app.listen(3000, () => {
    console.log('[demo] static-use-middleware is starting at port 3000')
  })
  ```

  