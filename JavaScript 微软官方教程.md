# JavaScript 官方教程

>- https://github.com/microsoft/beginners-intro-javascript-node
>- https://developer.mozilla.org/en-US/docs/Web/JavaScript

## 1 JavaScript on the Client vs JavaScript on the Server

### 1.1 JavaScript on the client

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
    	alert("Hello, JavaScript!");    
    </script>
	//
    <script src="./demo01.js"></script>
</body>
</html>
```

- 运行在浏览器的JS通常用来构建用户的某种页面，像运行在浏览器中的JS会访问叫做文档对象模型的东西，DOM。DOM只是JS在浏览器可以访问的函数和对象。
- npm：第三方包。包含你能想到的所有东西的包。

### 1.2 JavaScript on the server

- 服务器端没有浏览器或script标签，要在服务器端安装Node.js。通过node命令能执行任何JS文件，直接通过终端或命令行执行JS。

## 2 Setting up your toolbox

### 2.1 Visual Studio Code



### 2.2 VS Code Extensions

- ESLint
  - Find and fix problems in your JavaScript code before it hits production
- Prettier
  - Automatic code formatter, so you don't have to care about that extra space。
- JavaScript (ES6) code snippets:
  - Avoid redundant typing with shortcuts to most used code fragments。

### 2.3 Node.js

- 可以使用node.js来执行代码脱离浏览器。Node.js会提供你需要构建在你的JS应用中的，不管是否在浏览器中运行。

### 2.4 Install Node.js using NVM 
(manage updates and multiple environments)
  - For macOS,Linux or WSL users
    - https://github.com/nvm-sh/nvm
  - For Windows users: 
    - https://github.com/coreybutler/nvm-windows

- 安装
  - 搜索nvm.sh
  - 搜索nvm-windows



## 3 Manage your JavaScript Packages

### 3.1 What is a package?

- a package is a reusable bundle of code and/or assets.

### 3.2 NPM

- World's largest software registry

### 3.3 Why packages are important?

```javascript
var Koa = require('koa');
var app = new Koa();

app.get('/',(req,res)=>{
     res.send('Hello World!');
 });

app.listen(3000);
console.log('app started at port 3000...');
```

- 如果不使用koa包，我们不得不编写更多代码，来处理HTTP请求路由、创建HTTP响应、来处理header格式、数据转换等很多复杂厌烦的任务。

### 3.4 package.json

Every project's starting point

- Metadata
  - your project's name,version,description,author,repository,license,etc.
- Dependencies
  - the list of packages used by your project。
- Scripts:
  - commands to automate your development tasks。

### 3.5 Create a new project

```bash
npm init
```

- The npm init command allows to create a package.json file。
- you can use the -y option to skip the prompts。

```bash
npm init -y
```

### 3.6 Install a package

```bash
npm install <package_name>
```

- Download and add package to **dependencies**

```bash
npm install --save-dev <package_name>
```

- Download and add package to **devDependencies**

### 3.7 dependencies vs devDependencies

- dependencies 
  - 程序需要为了能在生产中运行的包
- devDependencies
  - 在开发应用中协助你的包，像库、代码格式化或编译工具，这些只在开发中有用，不会想在生产中用到。

### 3.8 实操包

```bash
npm init -y
```



```bash
npm install --save-dev prettier
//修改package.json文件
"scripts":{
	"format":"prettier --write *.js"
}
//在刚写完格式很乱的代码上，运行该命令
npm run format
//不过这一点和VS Code提供的扩展效果相同
```



```bash
//修改package.json文件
"scripts":{
	"start": "node index.js"
	"format":"prettier --write *.js"
}
//start 是关键脚本，所以指令不需要全写npm run...
npm start
```



```bash
// dotenv包允许你来管理应用配置，包括秘密
//如访问数据库的凭证不想被传到仓库中人人都可以看到，可以通过环境变量取代代码中固定值。
npm install dotenv

//重新设置监听端口值
require("dotenv").config();
const port = process.env.PORT;

//创建.env文件
PORT= 5000

//确保.env文件不被提交到git仓库中，为此要创建.gitignore文件
.env
node_modules/#是dependencies所有添加到你项目中会被下载的
```



## 4 Functions: 5 Things To Know!

### 4.1 Basic: What are Functions?

- 函数是基本的代码块，用来执行一系列语句，将它们抽象为一个可复用函数，会得到两个好处。可读性和复用性、可维护性。

### 4.2 Syntax: Define & Invoke your functions

- `function`  keyword
- `funtion name`  is used to invoke functions
- `funtion parameters` are placeholders for input
- closing `brace` terminates function(if no return statements are encountered)
- function `return`  defines exit points,placeholder for output(result) data.

### 4.3 Naming: Conventions & Tips

#### Like variable name conventions

- Use alphabets,numbers,$and_
- No other special characters allowed.

#### Function ".name" property

- returns the name of the function
- returns "anonymous" for anonymous functions

```javascript
function sayHello(){}
console.log(sayHello.name);	//sayHello
```

#### Anonymous functions

- no covered in this module

### 4.4 Parameters: Providing inputs

#### Are placeholders for input values

#### Arguments type/numbers are not checked against definitions

- Missing values will be set to undefined!
- This can have unanticipated results if you don't validate your arguments within the funtion.

### 4.5 Return: Providing outputs,exit points

#### Exit point for functions

- The return statement is optional.

  There is an implicit return after the last statement defined within the {}

- Use return statements to return relevant output (results) to the invoker.

- if no return statement is defined, the returned result is undefined.

## 5 Arrow Function
-  Changes to the *this* context


- Support implicit return values


- Must be assigned to a variable, or immediately used


- Reduction in characters typed

### 5.1 this context

This context in a traditional JavaScript functions is defined where that functions is called, whereas in an arrow function it's inherited from parent scope.

？？？？？？

- inherited from parent scope
- Cannot be changed
- Maintained when function is passed as a reference

### 5.2 Implicit Return

- Single line functions return statement result 

- Use {} to introduce multiple lines

## 6 JSON

### 6.1 What is JSON?

#### JSON=JavaScript Object Notation

- Language independent and user-readable.

- Depends only on 2 data structures that are found in all modern programming languages.

  - Collection of **name-value pairs**

  - Ordered list **of values**

  >
  >
  >因为键值对只能是单一序列化对象，如果想要在单行字符串装入多个对象，需要使用数组。

### 6.2 JSON Format

- Data in name-value pairs that are separated by commas
- {} enclose single objects
- [] enclose multi-item arrays

### 6.3 stringify: Serialize Object to JSON

- Serialize a JavaScript Object into an equivalent text string

- Serialize a collection of JavaScript Objects into an ordered list

### 6.4 parse: Deserialize JSON to Object

- Deserialize a JSON-formatted text string into a JavaScript object
- Deserialize a JSON-formatted text string into a JavaScript array

> 如何将JSON转换为函数对象？
>
> - 实现的最好方式是用结构体，能够选择序列化的属性和数据，然后转回定义的函数中，要用自定义构造函数。？？？？？

## 7 Objects: 6 Things To Know

### 1. Basics: What are Objects

- Representations of real world objects-using code!
- JavaScript objects have associated properties
- JavaScript objects have associated methods

### 7.2 Syntax: Object Definition

- Collection of unordered name=value pairs!

- This is an example of an object literal

  It's the easiest way to create a JavaScript object at definition

  Just enclose name-value pairs in {}--separated by commas

- You can also create objects in other ways

  using what's called constructors and new keyword

### 7.3 Creation: literals & Constructors

有两种创建对象的方式：使用字面量和构造函数。字面量使用简单，但构造体可以有很多属性，方便让你执行继承之类事情。(原型链)

### 7.4 Properties: Associated Variables

- Properties have immutable names and mutable values

  Naming uses the same conventions as variables

- Access Approach 1: Dot notation

  Use: <object>.<name>(like with namespaces)

- Access Approach 2: Bracket notation

  Use: <object>["name"] (like with hashmaps)

### 7.5 Methods: Associated Functions

- Methods are special properties whose values are function definitions

- Access methods just like properties

  ```javascript
  Ex: book.checkIn gives [Function:checkIn]
  Ex: book["checkIn"] gives [Function: checkIn]
  ```

- Invoke methods just like functions

  ```javascript
  Ex: book.checkIn() excutes that method
  Ex: book["checkIn"]() does likewise
  ```

### 7.6 "this": Associated Context

> *this* is a keyword that sets the runtime context for the function.
>
> It's a way for the function to be able to get access to other data or functions in it's runtime scope.

- Functions need access to runtime context.

  (Object) methods may need access to peer property values

  (Standalone) functions may use global variables in execution 

- "this" maps to relevant context at runtime

  - Maps to enclosing object-- for object methods
  - Maps to global--for standalone functions
  - May be undefined(strict mode) or changed(e.g., use apply)??????

- About the global object

  - defaults to window object in browser runtime

  - defaults to window object in browser runtime
  - use "globalThis" to reference the global object consistently

>
>
>当你在函数中使用this引用函数运行时上下文。
>
>- 如果函数存在于对象中，接着this的运行时上下文就会绑定到对象自身；
>- 如果在对象外使用，运行时上下文会绑定到全局作用域，this就会返回全局对象。



```javascript
const bookObj = {
    checkIn: function(){
        return this;
    }
}
console.log("\n\n this is:",bookObj.checkIn());//this is: {checkIn:[Function: checkIn]}
console.log(bookObj.checkIn() === globalThis);// false

function anotherCheckIn(){
    return this;
}
console.log("\n\n this is:",anotherCheckIn());
console.log(anotherCheckIn() === globalThis);
```









