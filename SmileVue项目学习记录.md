

# SmileVue项目学习记录

[TOC]

> 文档资源：
>
> - https://jspang.com/detailed?id=61

## 1 front-end



### 1.1 Vant

https://vant-contrib.gitee.io/vant

#### 1.安装

​	```npm install vant --save ```    

or

```npm install vant --save --registry=https://registry.npm.taobao.org```

#### 2.引入

- 全局：

  ```javaScript
  import Vant from 'vant'
  import 'vant/lib/vant-css/index.css'
  Vue.use(vant)
  ```

- 按需引入：

  vant是支持babel-plugin-import引入的，它可以让我们按需引入组件模块，并且不用管理我们的样式，现在Vue项目组件库的主流引入方法。 **安装babel-plugin-import\**

  ```npm install babel-plugin-import --save-dev```

  在.babelrc中配置plugins（插件）

  ```
  "plugins": [
      "transform-vue-jsx", 
      "transform-runtime",
      ["import",{"libraryName":"vant","style":true}]
    ]
  ```

#### 3  使用



### 1.2 iconFont



### 1.3 easyMock

- easyMock失效，改用https://www.fastmock.site





### 1.4 vue-awesome-swiper

#### 1 安装vue-awesome-swiper

​	注意查看官方文档。 github地址：https://github.com/surmon-china/vue-awesome-swiper

​	刚开始npm install就貌似有问题，引入需改成import 'swiper/css/swiper.css'

#### 2 详解

作为一个Vuer，`vue-awesome-swiper`组件在开发中是经常使用的，所以个人觉的是有必要详细了解一下它的用法的。他可以作轮播图，可以作滚动。

- 一个最简单的轮播图

```javascript
<template>
  <div >
      <swiper>
        <swiper-slide class="swiper-slide" v-for="(item, index) in slide" :key="index">
           Slide {{item}} 
        </swiper-slide>
      </swiper>
  </div>
</template>

<script>
    import 'swiper/dist/css/swiper.css' 
    import { swiper, swiperSlide } from 'vue-awesome-swiper'
    export default {
        data() {
            return {
                slide: [1,2,3,4,5,6]
            }
        },
        components:{swiper,swiperSlide}
    }
</script>
```

- 添加分页器

  作这类轮播图效果，现在主流的都是添加上分页器的，就是下方的小圆点，配置这些就需要在`swiper`标签上使用options属性来进行配置了。 我们现在data里进行配置，代码如下：

  ```javascript
  <template>
    <div >
        <swiper :options="swiperOption">
          <swiper-slide class="swiper-slide" v-for="(item, index) in slide" :key="index">
             Slide {{item}} 
          </swiper-slide>
          <div class="swiper-pagination" slot="pagination"></div>
        </swiper>
    </div>
  </template>
  
  <script>
      import 'swiper/dist/css/swiper.css' 
      import { swiper, swiperSlide } from 'vue-awesome-swiper'
      export default {
          data() {
              return {
                  slide: [1,2,3,4,5,6],
                  swiperOption:{
                      pagination:{
                          el:'.swiper-pagination'
                      }
                  }
              }
          },
          components:{swiper,swiperSlide}
      }
  </script>
  ```

- 竖屏切换效果

  在配置项里直接配置`direction`就可以了，配置竖屏代码如下。

```javascript
swiperOption:{
    direction:'vertical',
    pagination:{
        el:'.swiper-pagination'
    }
}
```

- 



### 1.5 首页楼层组件(Component)

```javascript
<template>
    <div>
        <!--floor one area-->
        <div class="floor">
			<div class="floor-title"> {{floorTitle}} </div>
            <div class="floor-anomaly">
                <div class="floor-one"><img :src="floorData0.image" width="100%" /></div>
                <div>
                    <div class="floor-two"><img :src="floorData1.image" width="100%" /></div>
                    <div><img :src="floorData2.image" width="100%" /></div>
                </div>
            </div>

            <div class="floor-rule">
                <div v-for="(item ,index) in floorData.slice(3)" :key="index">
                    <img :src="item.image" width="100%"/>
                </div>
            </div>

        </div>
    </div>
</template>

<script>
    export default {
        props:['floorData','floorTitle'],
        data() {
            return {
                floorData0:{},
                floorData1:{},
                floorData2:{}
            }
        },
        created(){
            //这里写得不到数据，应为数据是延迟返回的

        },
        watch:{
            floorData:function(val){
                console.log(this.floorData)
                this.floorData0=this.floorData[0]
                this.floorData1=this.floorData[1]
                this.floorData2=this.floorData[2]
            }
        }

    }
</script>

<style scoped>
  .floor-anomaly{
      display: flex;
      flex-direction:row;
      background-color: #fff;
      border-bottom:1px solid #ddd;
  }
  .floor-anomaly div{
     width:10rem;

     box-sizing: border-box;
     -webkit-box-sizing: border-box;
  }
  .floor-one{
      border-right:1px solid #ddd;

  }
  .floor-two{
      border-bottom:1px solid #ddd;
  }

  .floor-rule{
      display: flex;
      flex-direction: row;
      flex-wrap:wrap;
      background-color: #fff;

  }
  .floor-rule div{
      -webkit-box-sizing: border-box;
      box-sizing: border-box;
      width:10rem;
      border-bottom:1px solid #ddd;
  }
  .floor-rule div:nth-child(odd){
      border-right: 1px solid #ddd;
  }
.floor-title{
      text-align:center;
      font-size:14px;
      height: 1.8rem;
      line-height: 1.8rem;
  }
</style>
```

### 1.6 Filter在实战中的使用



### 1.7 VSCode 扩展



## 2 back-end

### 2.1 Koa

- 使用```npm init -y```生成并初始化package.json文件。

- 安装koa```npm install --save koa```

  ```javascript
  const Koa = require('koa')
  const app = new Koa()
  
  app.use(async(ctx)=>{
      ctx.body = '<h1>hello Koa2</h1>'
  })
  
  app.listen(3000,()=>{
      console.log('[Server] starting at port 3000')
  })
  ```

  - 使用`node index.js`来启动服务，然后在浏览器中输入http://loacalhost:3000`，如果正常显示hell koa2 说明我们的koa2已经安装成功。





#### koa2-cors

- 问题：ctx.request.body显示undefind？
- 解决：middleware的顺序很重要，这个koa-bodyparser必须在router之前被注册到app对象上.





### 2.2 MongoDB

​	http://jspang.com/detailed?id=36



### 2.3 Robo3

​	图形界面



### 2.4  Mongoose

#### 连接数据库

​	Mongoose就是Koa和MongoDB的粘合材料（其实也相当于一个驱动），帮助我们完成数据库的操作和通讯。要实现通讯第一步就是实现粘合，软件里叫做连接数据库。Mongoose是一个开源的封装好的实现Node和MongoDB数据通讯的数据建模库。

- 使用npm安装:```npm install mongoose --save```



#### Schema

​	Schema是一种以文件形式存储的数据库模型骨架，无法直接通往数据库端，也就是说它不具备对数据库的操作能力。Schema是以key-value形式Json格式的数据。



## 3 用户登录注册模块

### 3.1 业务逻辑（功能需求）

#### 1. 注册

用户跳转到注册页面，输入用户信息，输入信息，点击提交，前端先进行check验证，注意注册的防重复提交：

- 若通过则使用axiosRegisterUser()方法传递给后台，
  - 若response.data.code == 200，提示注册成功，跳转到首页。
  - 否则打印出response的message，并提示注册失败，且设置openLoading=false。
  - 捕捉到error，提示注册失败，且设置openLoading=false。
- 若未通过check验证，给出相应的提示信息。



```
registerAction(){
    this.checkForm() && this.axiosRegisterUser()
},
```

#### 2. 登录

> 移动端的应用有一个特殊的地方，就是当用户登录一次后，下次就不用登录了。这时候登录的信息是存储到了本地的LocalStorage里了。这个操作要等取得正确的登录状态以后再执行，也就是要在axios返回了登录成功结果以后执行。

用户跳转到登录页面，首先进入created():验证LocalStorage是否存在用户信息，若存在则提示"已登录"，直接跳转到首页。

用户输入信息，点击登录，前端进行check验证，

- 若通过则使用axiosRegisterUser()方法传递给后台，
  - 若response.data.code == 200，保存用户登录状态，提示登录成功，跳转到首页。
  - 捕捉到error，提示登录状态保存失败，且设置openLoading=false。



```javascript
//*****注册用户的实行方法*****
            LoginAction(){

                this.checkForm() && this.axiosLoginUser()
            },
```

### 3.2 前端业务逻辑

#### 1. 注册

```javascript
axiosRegisterUser(){
    axios({
        url:url.registerUser,
        method:'post',
        data:{
            userName:this.username,
            password:this.password,
            openLoading: false, //是否开启用户的Loading
             usernameErrorMsg:'',   //当用户名出现错误的时候
        passwordErrorMsg:'',   //当密码出现错误的时候
        }
    }).then(response =>{
        console.log(response)
        //如果返回code为200，代表注册成功，我们给用户作Toast提示
        if(response.data.code == 200){
            Toast.success('注册成功')
        }else{
            console.log(response.data.message)
            Toast.fail('注册失败')
        }
        console.log(response.data.code)
    })
    .ccatch((error)=>{
        Toast.fail('注册失败')
    })
}
```



#### 2.  登录

我们保存了用户登录状态以后，就有了一个是否登录的依据，然后我们就不会重复登录了，我们在已进入登录页面的`creadted`声明周期里，就判断是否已经登录。

```javascript
created(){
    if(localStorage.userInfo){
            Toast.success('您已经登录')
            this.$router.push('/')
    }
},
```

```javascript
new Promise((resolve,reject)=>{
        localStorage.userInfo={userName:this.username}
        setTimeout(()=>{
            resolve()
        },500)
}).then(()=>{
        Toast.success('登录成功')
        this.$router.push('/')
}).catch(err=>{
        Toast.fail('登录状态保存失败')
        console.log(err)
})
```


```javascript
axiosLoginUser(){
        //先把按钮进行loading状态，防止重复提交
        this.openLoading = true

    axios({
        url: url.login,
        method: 'post',
        data:{
            userName:this.username,
            password:this.password 
        }
    })
    .then(response => {
        console.log(response)
        if(response.data.code==200 && response.data.message){

            Toast.success('登录成功')
            this.$router.push('/')
        }else{
            Toast.fail('登录失败')
            this.openLoading = false
        }

    })
    .catch((error) => {   
        console.log(error)
        Toast.fail('登录失败')
        this.openLoading = false
    })

},
```



### 3.3 服务端业务逻辑

#### 1. 注册

```
router.post('/register',async(ctx)=>{

//取得MOdel
const User = mongoose.model('User')
//从前端接收的POST数据封装成一个新的User对象
let newUser = new User(ctx.request.body)

//用mongoose的save方法直接存储，然后判断是否成功，返回相应的结果
await newUser.save().then(()=>{
//成功返回code=200,并返回成功信息
ctx.body={
	code:200,
	message:'注册成功'
}
}).catch(error=>{
//失败返回到code=500,并返回错误信息
ctx.body={
	code:500,
	meaasqg:error
}
})

})
```



#### 2. 登录

需要在Shema中制作一个比对的实例方法，这个方法就是比对我们加盐加密后的密码的。在

```
userSchema.methods = {
    //密码比对的方法
    comparePassword:(_password,password)=>{
        return new Promise((resolve,reject)=>{
            bcrypt.compare(_password,password,(err,isMatch)=>{
                if(!err) resolve(isMatch)
                else reject(err)
            })
        })
    }
}
```

上面的代码声明了一个实例方法，方法叫做`comparePassword`，然后传递两个参数，一个是客户端密码，一个是数据库取出来的密码。用`bcrypt`提供的compare方法就可以比对，最后包装成`Promise`返回就可以了。



```
/*登录的实践 */
router.post('/login',async(ctx)=>{
    //得到前端传递过来的数据
    let loginUser = ctx.request.body
    console.log(loginUser)
    let userName = loginUser.userName
    let password = loginUser.password
    //引入User的model
    const User = mongoose.model('User')
    //查找用户名是否存在，如果存在开始比对密码
   await User.findOne({userName:userName}).exec().then(async(result)=>{
        console.log(result)
        if(result){
           //console.log(User)
            //当用户名存在时，开始比对密码
            let newUser = new User()  //因为是实例方法，所以要new出对象，才能调用
            await newUser.comparePassword(password,result.password)
            .then( (isMatch)=>{
                //返回比对结果
                ctx.body={ code:200, message:isMatch} 
            })
            .catch(error=>{
                //出现异常，返回异常
                console.log(error)
                ctx.body={ code:500, message:error}
            })
        }else{
            ctx.body={ code:200, message:'用户名不存在'}
        }

    }).catch(error=>{
        console.log(error)
        ctx.body={ code:500, message:error  }
    })
})
```



## 4 商品详情





## 5 购物车



## 6  底部导航

```vue
<template>
<div>
	<div class="main-div">
        <router-view/>
    </div>

    
         
    
</div>

</template>
```



