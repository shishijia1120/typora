# 微软Node.js教程



## 8 How to add and remove a dependency in a Node.js app

- ```bash
  npm install express
  ```

  a node modules folder has been created on my file system and this is what the node packages are installed.

- opening or editing a **gitignore** file and add to that your node_modules folder

  Why ?

  - Once you started having a lot of dependencies in your project, this folder can grow up to many gigs of data and the packages and it will often change as you update them. so it's really not a good idea to have right in your Git repo.
  - The  second reason is that if a coworker checks out the project by just typing **npm install** all the dependencies mentioned in the package.json will be resotred and so there's really no need to commit them.

-  **NPM**

  - npm stands for Node Package Manager

  - npm website npmjs.com (if you open up the npm website npmjs.com), you can search for any packages by name.

    ```bash
    npm install --save-dev jest
    ```

  - you can remove a package with the command **npm unistall**

    ```bash
    npm uninstall jest
    ```

    Now, jest has been removed from both the node modules folder and our package.json file.

    

## 17 How to build a web API with Node.js and Express

- Express

  - Express is a package library that will allow you to, relatively quickly stand up our RESTful API endpoints and even go ahead and create a fully functional server-side based web application.

    ```bash
    npm install express --save
    ```

  - body-parser

    - body-parser which is going to help us manage the information that's going to be sent up to the API that we're going to be building.
    - what the body-parser is going to do is it's going to help us read the information that the user has sent up to the server or the client has sent up to the server.

    ```bash
    npm install body-parser --save
    ```

  - cors

    - cors which will allow us to control where requests can come from 

    - what cores will do is basically give us control over where requests are allowed to come from when they're being sent via a browser. That by default you're only allowed through a browser to be able to access APIs that are on the server from which the page was rendered.

    - By using cores, we can say Hey this could come from somewhere else .That allows us to have one site that's going to be spitting out the client and then the other that's going to be managing the server.

      （允许设置某个站点只有客户端可以访问，其他正常和服务器交互）

    ```bash
    npm install cors --save
    ```

  - nodemon

    - nodemon which is a little helper function or helper package that will allow us to automatically restart our application every time that we make a change. It's great for development.

    ```bash
    npm install nodemon --save-dev
    ```

  - configure

    ```bash
    //configure app
    app.use(bodyParser.urlencoded({extended: true}));
    app.use(bodyParser.json());
    
    app.use(cors({origin:/http:\/\/localhost/}));
    app.options('*',cors());
    ```

## 18 How to create a GET API with Node.js and Express

- how we can start to configure routes inside of our Express App

  - A route, at its core is how it is that a client or user can request something ,be a data be an operation or otherwise from our application. Basically, what's going to happen is the client will specify a URL. They'll maybe send up some information and then that will cause a function to execute in response to that . 


- set up a get route

  - the way that we're going to set this up is we're going to create a router here.

    ```bash
    const router = express.Router();
    router.get('/',handle)
    ```

  - The handle that we're going to give it is going to take two parameters:

    - request which is going to contain any information that was sent up from the user
    - response, which is how we're going to send information down to the user. 

      ```javascript
  router.get('/',(req, res)=>{
          res.send(`${package.description} -v${package.version}`)
    });
      ```

- Simple rest client

  - 安装VS Code 扩展simple rest client，模拟请求
- 打开命令面板，搜索simple rest client:new request
  

  
## 19 How to handle API routing with Node.js and Express

- set up a simulated little database 

  ```javascript
  // our sample database(json)
  const db = {
      'christopher':{
          'user':'christopher',
          'currency':'USD',
          'balance':100,
          'description':'A sample account',
          'transactions':[]
      }
  }
  ```

- set up a get route

  ```javascript
  router.get('/accounts/:user',(req,res)=>{
      const user = req.params.user;
      const account = db[user];
      if(!account){
          return res
                  .status(404)
                  .json({error:'User does not exist'});
              }
      return res.json(account);
  })
  ```

- Simple rest client

  - url：http://localhost:5000/api/accounts/christopher
  - response：{
        "user": "christopher",
        "currency": "USD",
        "balance": 100,
        "description": "A sample account",
        "transactions": []
    }

  ----

  - url：http://localhost:5000/api/accounts/ssj
  - respose：[object Object]（此处与视频不同????）



## 20 How to create an API that receive JSON with Node.js and Express

how you can allow a client to send information up to the server to create new items.

- set up a get route





- Simple rest client







## 21 How to create update and delete APIs with Node.js and Express

how we can complete our REST endpoint by adding in the ability to modify an item and to delete an item.

