## Promise入门到自定义

>尚硅谷课程:李强

## 1 Promise介绍与基本使用

### 1.1 Promise是什么?

#### 1.1.1 理解异步与回调

1. 抽象表达式：

   - Promise是一门新的技术(ES6规范)

   - Promise是JS中进行**异步编程**的新解决方案

     旧方案是单纯使用**回调函数**

2. 具体表达：

   - 语法：Promise是一个构造函数
   - 功能：Promise对象用来封装一个**异步操作**并可以获取其成功/失败的结果值

**异步编程(回调函数形式)**

- fs文件操作

  ```javascript
  require('fs').readFile('./index.html',(err,data)=>{})
  ```

- 数据库操作

- AJAX

  ```javascript
  $.get('/server',(data)=>{})
  ```

- 定时器

  ```javascript
  setTimeout(()=>{},2000);
  ```

>
>
>1. **单线程模型**
>   - js只在一个线程上运行，同时只能执行一个任务，其他任务都必须在后面排队等待。
>   - JavaScript引擎有多个线程，单个脚本只能在一个线程上运行(称为主线程)，其他线程都是在后台配合。
>   - 当IO操作很慢时，JavaScript完全不管IO操作，挂起处于等待中的任务，先运行排在后面的任务。等到IO操作返回了结果，再回过头，把挂起的任务继续执行下去。这种机制就是JavaScript内部采用的"事件循环"机制(Event Loop)。
>2.  **同步任务和异步任务**
>
>- 同步任务：没有被引擎挂起，在主线程上排队执行的任务。只有前一个任务执行完毕，才能执行后一个任务。
>- 异步任务：被引擎放在一边，不进入主线程而进入任务队列的任务。只有引擎认为某个异步任务可以执行了(比如Ajax操作从服务器得到了结果)，该任务(采用回调函数的形式)才会进入主线程执行。排在异步任务后面的代码，不用等待异步任务结束会马上运行。异步任务不具有"堵塞"效应。
>
>3. **任务队列和事件循环**
>
>- 任务队列：
>  - JavaScript运行时，除了一个正在运行的主线程，引擎还提供一个任务队列(task queue)，里面是各种需要当前程序处理的异步任务。(实际上，根据异步任务的类型。存在多个任务队列。)
>  - 首先，主线会去执行所有的同步任务。等到同步任务全部执行完，就会去看任务队列里面的异步任务。如果满足条件，那么异步任务就重新进入主线程开始进行，这时候它就变成同步任务了。等到执行完，下一个任务队列清空，程序结束执行。
>  - 异步任务的写法通常是回调函数。一旦异步任务重新进入主线程，就会执行对应的回调函数。
>  - 如果一个异步任务没有回调函数，就不会进入任务队列，也就是说，不会重新进入主线程，因为没有用回调函数指定下一步操作。
>- 事件循环：
>  - JavaScript引擎怎么知道异步任务有没有结果，能不能进入主线程呢？答案就是引擎在不停地检查，一边又一遍，只要同步任务执行完了，引擎就会检查那些挂起来的异步任务，是不是可以进入主线程了。这种循环检查的机制就是事件循环(Event Loop)。
>
>4. **异步操作的模式**
>
>- (1) 回调函数
>
>  函数f1和f2，编程的意图是f2必须等到f1执行完成，才能执行：
>
>  ```javascript
>  function f1(){
>      //...
>  }
>  function f2(){
>      //...
>  }
>  f1();
>  f2();
>  ```
>
>  问题：如果f1是异步操作，f2会立即执行，不会等到f1结束再执行。
>
>  解决办法：把f2写成f1的回调函数：
>
>  ```javascript
>  function f1(callback){
>      //...
>      callback();
>  }
>  function f2(){
>      //...
>  }
>  f1(f2);
>  ```
>
>  优点：简单，容易理解和实现；缺点：缺点不利于代码的阅读和维护，各个部分之间高度耦合，使得程序结构混乱、流程难以追踪(尤其是多个回调函数嵌套的情况)，而且每个任务只能指定一个回调函数。
>
>- (2) 事件监听
>
>
>
>- (3)  发布/订阅 



#### 1.1.2 Promise的状态改变

1. pending变为resolved / fullfilled

2. pending变为rejcted

   只有两种状态，且一个promise对象只能改变一次。

   无论变为成功还是失败，都会有一个结果数据

   成功的结果数据一般称为value，失败的结果数据一般称为reason

3. 实例对象中的一个属性 [PromiseState]

   - pending： 未决定的
   - resolved / fullfilled:    成功
   - rejected:     失败

4. 实例对象中的另一个属性 [PromiseResult]

   保存着对象[成功/失败]的结果

   - resolve
   - reject

```javascript
console.log(p);=>[[promiseState]]"rejected"
				[[promiseResult]] xxx
```

#### 1.1.3 Promise的基本流程

![image-20210324102146692](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210324102146692.png)



### 1.2 为什么使用Promise

- 指定回调函数的方式更加灵活
  - 旧的：必须在异步启动任务前指定
  - promise: 启动异步任务=>返回promise对象 =>给promise对象绑定回调函数(甚至可以在异步任务结束后指定多个)
- 支持链式调用，可以解决回调地狱问题



### 1.3 如何使用Promise?

#### 1.3.1 API

##### 1. Promise构造函数：Promise(excutor){}

   - executor函数： 执行器 (resolve,reject)=>{}
   - resolve函数：内部定义成功时我们调用的函数value=>{}
   - reject函数：内部定义失败时我们调用的函数reason=>{}

   说明：executor会在Promise内部立即同步调用，异步操作在执行器中执行

##### 2. Promise.prototype.then方法:（onResolved,onRejected)=>{}

   - onResolved函数：成功的回调函数(value)=>{}
   - onRejected函数：失败的回调函数(reson)=>{}

   说明：指定用于得到成功value的成功回调和用于得到失败eason的失败回调返回一个**新的promise对象**

   

##### 3. Promise.prototype.catch方法:(onRejected)=>{}

   - onRejected函数：失败的回调函数(reason)=>{}

##### 4. Promise.resolve方法: (value)=>{}

   - value: 成功的数据或promise对象

     说明：返回一个成功或失败的**promise对象**

```javascript
let p1 = Promise.resolve(521);
//如果传入的参数为非Promise对象，则返回的结果为成功promise对象
//如果传入的参数为Promise对象，则参数的结果决定了ressolve的结果
let p2 = Promise.resolve(new Promise((resolve,reject)=>{
    resolve('OK');//[[PromiseState]]:"fullfilled"[[PromiseResult]]:"OK"
    or
    reject('error'); //[[PromiseState]]:"rejected"[[PromiseResult]]:"error"
}))
p2.catch(reason=>{
    console.log(reason);
})
conslole.log(p1);
```

##### 5. Promise.reject方法:(reason)=>{}

   - reason: 失败的原因

     说明：返回一个失败的promise对象

   ```javascript
   let p = Promise.reject(521);
   let p2 = Promise.reject('abc');
   let p3 = Promise.reject(new Promise((resolve,reject)=>{
       resolve('OK');
   }))
   
   conslole.log(p1);//返回的结果永远都是失败的，传入什么失败的结果就是什么
   ```

##### 6. Promise.all方法:(promises)=>{}

   - promises:包含n个promise的数组

     说明：返回一个新的promise，只有所有的promise都成功才成功，只要有一个失败了就直接失败

   ```javascript
   let p1 = new Promise((resolve,reject)=>{
       resolve('OK');
   })
   let p2 = Promise.resolve('Success');
   let p3 = Promise.resolve('Oh Yeah');
   let p4 = Promise.reject('Error');
   
   const result = Promise.all([p1,p2,p3]);
   console.log(result);//[[PromiseState]]:"fullfilled"[[PromiseResult]]:Array(3)
   					//0:"OK" 1:"Success" 2:"Oh Yeah" length:3
   
   const result2 = Promise.all([p1,p2,p4]);
   console.log(result2);//[[PromiseState]]:"rejected"[[PromiseResult]]:"Error"
   ```

##### 7. Promise.race方法：(promises)=>{}

   - promises:包含n个promises的数组

     说明：返回一个新的promise，第一个完成的promise的结果状态就是最终的结果状态

   ```javascript
   let p1 = new Promise((resolve,reject)=>{
       resolve('OK');
   });
   let p2 = Promise.resolve('Success');
   let p3 = Promise.resolve('Oh Yeah');
   
   //调用
   const result = Promise.race([p1,p2,p3]);//p1先改变状态
   console.log(result);//[[PromiseState]]:"fullfilled"[[PromiseResult]]:'OK'
   
   let p4 = new Promise((resolve,reject)=>{
     setTimeout(()=>{
           resolve('OK');
     },1000);
   });
   const result2 = Promise.race([p4,p2,p3]);//p2先改变状态
   console.log(result2);//[[PromiseState]]:"fullfilled"[[PromiseResult]]:'Success'
   ```

#### 1.3.2 promise的几个关键问题

##### 1. 如何改变promise的状态？

```javascript
let p = new Promise((resolve,reject)=>{
   //1. resolve函数
    resolve('ok'); //pending => fullfilled(resolved)
    //2. reject函数
    reject('error');//pending => rejected
    //3. 抛出错误
    throw '出问题了';
});
console.log(p);
```

##### 2. 一个promise指定多个成功/失败回调函数，都会调用吗？
   - 当promise改变为对应状态时都会调用

```javascript
let p = new Promise((resolve,reject)=>{
    resolve('OK');
});

//指定回调-1
p.then(value=>{
    console.log(value);
});

//指定回调-2
p.then(value=>{
    alert(value);
});
```

##### 3. 改变promise状态和指定回调函数谁先谁后？
- (1) 都有可能，正常情况下是先指定回调函数再改变状态，但也可以先改变状态再指定回调

- (2) 如何先改变状态再指定回调？
  - 在执行器中直接调用resolve()/reject()
  - 延迟更长时间才调用then()
- (3) 什么时候才能得到数据？
  - 如果先指定的回调，那当状态发生改变时，回调函数就会调用，得到数据
  - 如果先改变的状态，那当指定回调时，回调函数就会调用，得到数据

```javascript
let p = new Promise((resolve,reject)=>{
   resolve('OK');//此时先改变状态，再指定回调，即执行then()方法
    or
   setTimeout(()=>{
       resolve('OK');//此时先执行then()方法，再改变状态
   },1000);
});

p.then(value=>{
    console.log(value);
},reason=>{
    
});
```

>
>
>then()方法？？？

##### 4. promise.then()返回的新的promise的结果状态由什么决定？

- (1) 简单表达：由then()指定的回调函数执行的结果决定
- (2) 详细表达：
  - 如果抛出异常，新promise变为rejected,reason为抛出的异常
  - 如果返回的是非promise的任意值，新promise变为resolved,value为返回的值
  - 如果返回的是另一个新promise,此promise的结果就会成为新promise的结果

```javascript
let p = new Promise((resolve,reject)=>{
    resolve('ok');
});
//执行then方法
let result = p.then(value=>{
    //console.log(value);
    //1. 抛出错误
    //throw '出了问题'
    //2. 返回结果是非Promise类型的对象
    //return 521;
    //3. 返回结果是Promise类型的对象
    //return new Promise((resolve,reject)=>{
    //	resolve('success');
    //	reject('error');
	//});
},reason=>{
	console.warn(reason);         
});

console.log(result);
```

##### 5. promise如何串连多个操作任务？(重点)

- (1) promises的then()返回一个新的promise，可以开成then()的链式调用
- (2) 通过then的链式调用串连多个同步/异步任务

```javascript
let p = new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve('OK');
    },1000);
});

p.then(value=>{
    return new Promise((resolve,reject)=>{
        resolve("success");
    });
}).then(value=>{
    console.log(value);//'success'
}).then(value=>{
    console.log(value);//'undefined'
})
```

>
>
>????

##### 6. promise异常穿透?

- (1) 当使用promise的then链式调用时，可以在最后指定失败的回调，
- (2) 前面任何操作出了异常，都会传到最后失败的回调中处理

```javascript
let p = new Promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve('OK');
        or
        reject('Err');
    },1000);
});

p.then(value=>{
    console.log(111);
    or
    throw '失败啦!';
}).then(value=>{
    console.log(222);
}).then(value=>{
    console.log(333);
}).catch(reson=>{
    console.warn(reason);
});
```

##### 7. 中断Promise链?

- (1) 当使用promise的then链式调用时，在中间中断，不再调用后面的回调函数
- (2) 办法：在回调函数中返回一个pending状态的promise对象

```javascript


```



## 1.4 Promise实践练习

#### 1.4.1 fs读取文件

```javascript
//回调函数形式
const fs = require('fs');
fs.readFile('./resource/content.txt',(err,data)=>{
    //如果出错，则抛出错误
    if(err) throw err;
    //输出文件内容
    console.log(data.toString());
})

//Promise形式
let p = new Promise((resolve,reject)=>{
    fs.readFile('./resource/content.txt',(err,data)=>{
    //如果出错
    if(err) reject(err);
    //如果成功
    resolve(data);
});
});

//调用then
p.then(value=>{
    console.log(value.toString());
},reason=>{
   console.log(reason); 
});
```

#### 1.4.2 AJAX请求

```javascript
//回调函数形式
btn.addEventListener('click',function(){
    //1.创建对象
    const xhr = new XMLHttpRequest();
    //2. 初始化
    xhr.open('GET','https://api.apiopen.top/getJok');
    //3. 发送
    xhr.send()
    //4. 处理响应结果
    xhr.onreadystatechange = function(){
        if(xhr.onreadystatechange === 4){
            //判断相应状态码 2xx
            if(xhr.status >= 200 && xhr.status < 300)
                console.log(xhr.response);
        }
        else{
            console.log(xhr.status);
        }
    }
});

//Promise形式
const p = new Promise((resolve,reject)=>{
    //1.创建对象
    const xhr = new XMLHttpRequest();
    //2. 初始化
    xhr.open('GET','https://api.apiopen.top/getJok');
    //3. 发送
    xhr.send()
    //4. 处理响应结果
    xhr.onreadystatechange = function(){
        if(xhr.onreadystatechange === 4){
            //判断相应状态码 2xx
            if(xhr.status >= 200 && xhr.status < 300)
            	resolve(xhr.response);
        }
        else{
            reject(xhr.status);
        }
    }
});
//调用then方法
p.then(value=>{
    console.log(value);
},reason=>{
	 console.warn(reason);
});

```



## 2 自定义(手写)Promise

### 2.1 自定义整体结构

 





## 3 async与await

