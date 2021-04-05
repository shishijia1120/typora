# Flask Web开发实战(李辉)

>Werkzeug的官方文档：http://werkzeug.pocoo.org/docs/
>
>Click的官方文档：https://click.palletsprojects.com/en/7.x/
>
>Flask文档的Config章节：(https://flask.palletsprojects.com/en/1.1.x/config/





## 1 初识Flask

### 1.1 搭建开发环境

#### 1.1.1 Pipenv工作流

- **pipenv**是基于pip的Python包管理工具，它和pip的用法非常相似，可以看作pip的加强版，它的出现解决了旧的**pip+virtualenv+requirements.txt**的工作方式的弊端。具体来说，它是pip、Pipfile和Virtualenv的结合体，它让**包安装、包依赖管理和虚拟环境管理**更加方便，使用它可以实现高效的Python项目开发工作流。

1. **安装pip和Pipenv**

   pip是用来安装Python包的工具。如果你使用Python2.7.9及以上版本或Python3.4及以上版本，那么pip已经安装好了。可以使用下面的命令检查pip是否已经安装:

   ```bash
   $ pip --version
   ```

   ```python
   $ pip install <某个包的名称>
   ```

   这会从PyPI(Python Package Index，Python包索引)上下载并安装指定的包。

   > PyPI是一个Python包的在线仓库，后面我们会学习如何编写自己的Flask扩展，并把它上传到PyPI上。到时你就可以使用上面这条命令安装自己编写的包。

   现在使用pip安装Pipenv：

   ```bash
   $ pip install pipenv
   ```

   在LInux或macOS系统中使用sudo以全局安装：

   ```bash
   $ sudo pip install pipenv
   ```

   >PyPI中的包名称不区分大小写。出于方便的考虑，后面的安装命令都将使用小写名称。

   检查Pipenv是否已经安装:

   ```bash
   $ pipenv --version
   ```

2. **创建虚拟环境**

   在Python中，虚拟环境(virtual environment)就是隔离的Python解释器环境。通过创建虚拟环境，你可以拥有一个独立的Python解释器环境。
   
   虚拟环境通常使用Virtualenv来创建，但是为了更方便地管理虚拟环境和依赖包，我们将会使用集成了Virtualenv的Pipenv。为当前的项目创建虚拟环境：
   
   ```bash
   $ pipenv install
   ```





#### 1.1.2 安装Flask

```bash
$ pipenv install flask
```

>Pipenv会**自动帮我们管理虚拟环境**，所以在执行pipenv install安装Python包时，无论是否激活虚拟环境，包都会安装到虚拟环境中。这相当于在激活虚拟环境的情况下使用pip安装包。只有需要在全局环境下安装/更新/删除包，我们才会使用pip。

| 名称与版本   | 说明                                                         | 资源                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Jinja2(2.10) | 模板渲染引擎                                                 | 主页:http://jinja.pocoo.org/ 源码:https://github.com/pallets/jinja 文档：http://jinja.pocoo.org/docs/ |
| MarkupSafe   | HTML字符转义(escape)工具                                     |                                                              |
| Werkzeug     | WSGI工具集，处理请求与响应，内置WSGI开发服务器、调试器和重载器 | 主页:http://werkzeugrg.pocoo.org/源码:https://github.com/pallets/werkzeug 文档：http://werkzeug.pocoo.org/docs/ |
| click        | 命令行工具                                                   | 主页:http://github.com/pallets/click文档：http://click.pocoo.org/6/ |
| itsdangerous | 提供各种加密签名功能                                         |                                                              |

#### 1.1.3 集成开发环境

- 下载并安装PyCharm
- 创建项目
- 设置Python解释器





### 1.2 Hello Flask!

```bash
$ cd demos/hello
```

hello/app.py:最小的Flask程序

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return '<h1>Hello Flask!</h1>'
```



#### 1.2.1 创建程序实例

我们安装Flask 时，它会在Python解释器中创建一个flask包，我们可以通过**flask包的构造文件**导入所有开放的类和函数。先从flask包导入Flask类，这个类表示一个Flask程序。实例化这个类，就得到我们的程序实例app:

```bash
from flask import Flask
app = Flask(__name__)
```

- 传入Flask类构造方法的第一个参数是模块或包的名称，我们应该使用特殊变量\_\_name\_\_。Python会根据所处的模块来赋予\_\_name\_\_变量相应的值，对于我们的程序来说(app.py)，这个值为app。

>Flask类是Flask的核心类，它提供了很多与程序相关的属性和方法。在后面，我们会直接在程序实例app上调用这些属性和方法来实现相关功能。

#### 1.2.2 注册路由

在一个Web应用里，客户端和服务器端上的Flask程序的交互可以简单概括为以下几步：

- 用户在浏览器输入URL访问某个资源。
- Flask接收用户请求并分析请求的URL。
- 为这个URL找到对应的处理函数。
- 执行函数并生成响应，返回给浏览器。
- 浏览器接收并解析响应，将信息显示在页面中。

在上面这些步中中，大部分都由Flask完成，我们要做的只是建立处理请求的函数，并为其定义对应的URL规则。只需为函数附加app.route()装饰器，并传入URL规则作为参数，我们就可以让URL与函数建立关联。这个过程我们称为**注册路由(route)**，路由负责管理URL和函数之间的映射，而这个函数则被称为视图函数(view function)



。。。





>route()装饰器的第一个参数是URL规则，用字符串表示，必须以斜杠(/)开始。这里的URL是相对URL。

1. 为视图绑定多个URL

2. 动态URL

   ```python
   @app.route('/greet/<name>')
   def greet(name):
       return '<h1>Hello, %s!</h1>' % name
   ```

   因为URL中可以包含变量，所以我们将传入app.route()的字符串称为URL规则，而不是URL。Flask会解析请求并把请求的URL与视图函数的URL规则进行匹配。

   ```python
   @app.route('/greet',defaults={'name':'Programmer'})
   @app.route('/greet/<name>')
   def greet(name):
       return '<h1>Hello, %s!</h1>' % name
   ```

   等同于

   ```python
   @app.route('/greet/<name>')
   def greet(name='Programmer'):
       return '<h1>Hello, %s!</h1>' % name
   ```

### 1.3 启动开发服务器

Flask内置了一个简单的开发服务器(由依赖包Werkzeug提供），足够在开发和测试阶段使用。

在生产环境需要使用性能更好的生产服务器，以提升安全和性能。



#### 1.3.1 Run, Flask, Run!

Flask通过依赖包Click内置了一个CLI(Command Line Interface，命令行交互界面)系统。当我们安装Flask后，会自动添加一个flask命令脚本，我们可以通过flask命令执行内置命令、扩展提供的命令或是我们自己定义的命令。其中，flask run命令用来启动内置的开发服务器：

```bash
$ flask run
```

确保执行命令前激活了虚拟环境(pipenv shell)，否则需要使用pipenv run flask run命令启动开发服务器。

- 自动发现程序实例

  一般来说，在执行flask run命令运行程序前，我们需要提供程序实例所在模块的位置。我们在上面可以直接运行程序，是因为Flask会自动探测程序实例，自动探测在下面这些规则：

  - 从当前目类寻找app.py和wsgi.py模块，并从中寻找名为app或application的程序实例。
  - 从环境变量FLASK_APP对应的值寻找名为app或application的程序实例。

  如果你的程序主模块是其他名称，比如hello.py，那么需要设置环境变量FLASK_APP，将包含程序实例的模块赋值给这个变量。Linux或macOS系统使用export命令:

  ```bash
  $ export FLASK_APP=hello
  ```

  在Windows系统中使用set命令：

  ```bash
  > set FLASK_APP = hello
  ```

- 管理环境变量

  Flask的自动发现程序实例机制还有第三条规则：如果安装了python-dotenv，那么在使用flask run或其他命令命令时会自动从.flaskenv和.env文件中加载环境变量。

  >当安装了python-dotenv时，Flask在加载环境变量的优先级是：**手动设置的环境变量> .env中设置的环境变量>.flaskenv设置的环境变量。**

  除了FLASK_APP，在后面还会用到其他环境变量。我们可以使用python-dotenv管理项目的环境变量，首先使用Pipenv将它安装到虚拟环境：

  ```bash
  $ pipenv install python-dotenv
  ```

  在项目根目录下分别创建两个文件：.env和.flaskenv。.flaskenv用来存储和Flask相关的公开环境变量，比如FLASK_APP：而.env用来存储包含敏感信息的环境变量。

  ```bash
  SOME_VAR=1
  # 这是注释
  FOO="BAR"
  ```

  >.env包含敏感信息，除非是私有项目，否则绝对不能提交到Git仓库中。当你开发一个新项目时，记得把它的名称添加到.gitignore文件中，这回告诉Git忽略这个文件。gitignore文件是一个名为.gitignore的文本文件，它存储了项目中Git提交时的忽略文件规则清单。Python项目的.gitignore模板可以参考https://github.com/github/gitignore/blob/master/Python.gitignore。
  >
  >。。。。



- 使用PyCharm运行服务器



#### 1.3.2 更多的启动选项

- 使服务器外部可见‘

  我们在上面启动的Web服务器默认是对外不可见的，可以在run命令后添加--host选项将主机地址设为0.0.0.0使其对外可见：

  ```bash
  $ flask run --host=0.0.0.0
  ```

  这会让服务器监听所有外部请求。个人计算机(主机)一般没有公网IP(公有地址)，所以你的程序只能被局域网内的其他用户通过你的个人计算机的内网IP(私有地址)访问。

- 改变默认端口

  Flask提供的Web服务器默认监听5000端口，你可以在启动时传入参数来改变它：

  ```bash
  $ flask run --port=8000
  ```

  >执行flask run命令时的host和port选项也可以通过环境变量FLASK_RUN_HOST和FLASK)RUN_PORT设置。事实上，Flask内置的命令都可以使用这种模式定义默认选项值，即"FLASK_<COMMAND>__<OPTION>"，你可以使用flask--help命令查看所有可用的命令。

#### 1.3.3 设置运行环境

- 开发环境：指我们在本地编写和测试程序时的计算机环境；

- 生产环境：指的是网站部署上线供用户访问时的服务器环境。

根据运行环境的不同，Flask程序、扩展以及其他程序会改变相应的行为和设置。

为了区分程序运行环境，Flask提供了一个FLASK_ENV环境变量用来设置环境，默认为production(生产)。在开发时，我们可以将其设为development(开发)，这会开启所有支持开发的特性。为了方便管理，我们将把环境变量FLASK_ENV的值写入.flaskenv文件中：

```bash
FLASK_ENV=development
```

在开发环境下，调试模式(Debug Mode)将被开启，这时执行flask run启动程序会自动激活Werkzeug内置的调试器(debugger)和重载器(reloader)，它们会为开发带来很大的帮助。

>如果你想单独控制调试模式的开关，可以通过FLASK_DEBUG环境变量设置，设为1则开启，设为0则关闭，不过通常不推荐手动设置这个值。
>
>注意：在生产环境中部署程序时，绝不能开启调试模式。



- 调试器

  - Werkzeug提供的调试器非常强大，当程序出错时，我们可以在网页上看到详细的错误追踪信息，这在调试错误时非常有用。

  - 调试器允许你在错误页面上执行Python代码。单机错误信息右侧的命令行图标，会弹出窗口要求输入PIN码，也就是在启动服务器时命令行窗口打印出的调试器PIN码。
  - 。。。。。

- 重载器

  - 当我们对代码做了修改后，期望的行为是这些改动立刻作用到程序上。重载器的作用就是监测文件变动，然后重新启动开发服务器。

  - 默认会使用Werkzeug内置的stat重载器，它的缺点是耗电严重，而且准确性一般。为了获得更优秀的体验，我们可以安装另一个用于检测文件变动的Python库Watchdog，安装后Werkzeug会自动使用它来检测文件变动:

    ```bash
    $ pipenv install watchdog --dev
    ```

    - 因为这个包只在开发时才会用到，所以我们在安装时添加了一个--dev选项，这用来把这个包声明为开发依赖。在Pipfile文件中，这个包会被添加到dev-packages部分。
    - 不过，如果项目中使用了单独的CSS或JavaScript文件时，那么浏览器可能会缓存这些文件，从而导致对文件做出的修改不能立刻生效。在浏览器中，我们可以按下Ctrl+F5或Shift+F5执行硬重载(hard reload)，即忽略缓存并重载(刷新)页面。

### 1.4 Python Shell

在开发Flask程序时，我们并不会直接使用python命令启动Python Shell，而是使用flask shell命令：

```bash
$ flask shell
```

>注意：和其他flask命令相同，执行这个命令前我们要确保程序实例可以被正常找到。

>Python Shell可以执行exit()或quit()退出，在Windows系统上可以使用Ctrl+Z并按Enter退出；在Linux和macOS则可以使用Ctrl_D退出。

- 使用flask shell命令打开的Python Shell自动包含程序上下文，并且已经导入了app实例：

  ```bash
  >>> app
  <Flask 'app'>
  >>> app.name
  'app'
  ```

- 上下文(context)可以理解为环境。为了正常运行程序，一些操作相关的状态和数据和数据需要被临时保存下来，这些状态和数据被统称为上下文。在Flask中，上下文有两种，分别为:

  - 程序上下文
  - 请求上下文

### 1.5 Flask扩展

- 扩展：使用 Flask提供的API接口编写的Python库，可以为Flask程序添加各种各样的功能。
- 大部分Flask扩展用来集成其他库，作为Flask和其他库之间的薄薄一层胶水。
- 大部分扩展都会提供一个扩展类，实例化这个类，并传入我们创建的程序实例app作为参数，即可完成初始化过程。
- 通常，扩展会在传入的程序实例上注册一些处理函数，并加载一些配置。



以某扩展实现了Foo功能为例，这个扩展的名称将是Flask-Foo或Foo-Flask；程序包或模块的名称使用小写加下划线，即flask_foo(即导入时的名称)：用于初始化的类一般为Foo，实例化的类实例一般使用小写，即foo。

```python
from flask import Flask
from flask_foo import Foo

app = Flask(__name__)
foo = Foo(app)
```

### 1.6 项目配置

- 在一个项目中，你会用到许多配置：
  - Flask提供的配置
  - 扩展提供的配置
  - 程序特定的配置

这些配置变量都通过Flask对象的app.config属性作为统一的接口来设置和获取，它指向的Config类实际上是字典的子类，所以你可以像操作其他字典一样操作它。

>Flask内置的配置可以访问Flask文档的配置章节(https://flask.palletsprojects.com/en/1.1.x/config/)查看，扩展提供的配置也可以在对应的文档中查看。

- Flask提供了很多种方式来加载配置。比如，

  - 你可以像在字典中添加一个键值对一样来设置一个配置:

    ```bash
    app.config['ADMIN_NAME']='Peter'
    ```

    > 配置的名称必须是全大写形式，小写的变量将不会被读取。
  
	  使用update()方法则可以一次加载多个值： 

    ```python
     app.config.update(
        TESTING = True,
          SECRET_KEY='_5#yF4Q8z\n\xec]/'
      )
    ```
  
  - 还可以把配置变量存储在单独的Python脚本、JSON格式的文件或是Python类中。config对象提供了相应的方法来导入配置。
  
  >某些扩展需要读取配置来完成初始化操作，比如Flask-Mail，因此我们应该尽量将加载配置的操作提前，最好在程序实例app创建后就加载配置。

### 1.7 URL与端点

在Web程序中，URL无处不在。如果程序中的URL都是以硬编码的方式写出，那么将会大大降低代码的易用性。如果，你修改了某个路由的URL规则，那么程序里对应的URL都要一个一个进行修改。更好的解决办法是使用Flask提供的url_for()函数获取URL，当路由中定义的URL规则被修改时，这个函数总会返回正确的URL。

- 调用url_for()函数时，第一个参数为端点(endpoint)值。在Flask中，端点用来标记一个视图函数以及对应的URL规则。端点的默认值为视图函数的名称，至于为什么不直接使用视图函数名，而要引入端点这个概念，后面会说。

  ```python
  @app.route('/')
  def index():
      return 'Hello Flask!'
  ```

  这个路由的端点即视图函数的名称index，调用url_for('index')即可获取对应的URL，即"/"。

- 如果URL含有动态部分，那么我们需要在url_for()函数里传入相应的参数，以下面的视图函数为例：

  ```python
  @app.route('/hello/<name>')
  def greet(name):
      return 'Hello %s!' % name
  ```

  这时使用url_for('say_hello',name='Jack')得到的URL为"/hello/Jack"。

  >我们使用url_for()函数生成的URL是相对URL，只能在程序内部使用。如果想要生成供外部使用的绝对URL，可以在使用url_for()函数时，将_external参数设为True，这会生成完整的URL。

### 1.8 Flask命令

- 除了Flask内置的flask run等命令，我们也可以自定义命令。在虚拟环境安装Flask后，包含许多内置命令的flask脚本就可以使用了。

- 通过创建任意一个函数，并为其添加app.cli.command()装饰器，就可以注册一个flask命令。

  ```python
  @app.cli.command()
  def hello():
      click.echo('Hello, Human!')
  ```

  函数的名称即为命令名称，这里注册的命令即hello，你可以使用flask hello命令来触发函数。作为替代，你可以在app.cli.command()装饰器中传入参数来设置命令名称，比如app.cli.command('say-hello')会把命令名称设置为say-hello，完整的命令即flask say-hello。

  ```bash
  $ flask hello
  Hello,Human!
  ```

- 执行flask --help可以查看Flask提供的命令帮助文档，我们自定义的hello命令也会出现在输出的命令列表中。

  > 关于自定义命令的设置和功能可参考Click的官方文档(http://click.pocoo.org/6/)。

### 1.9 模板与静态文件

。。。。



### 1.10 Flask与MVC架构

- 在MVC架构中，程序被分为三个组件：数据处理(Model)、用户界面(View)、交互逻辑(Controller)。如果套用MVC架构的内容，那么Flask中视图函数的名称其实并不严谨，使用控制器函数(Controller Function)似乎更合适些，虽然它也附带处理用户界面。(严格来说，Flask并不是MVC架构的框架，因为它没有内置数据模型支持。)

- 如果想要使用Flask来编写一个MVC架构的程序，那么视图函数可以作为控制器(Controller)，视图(View)是使用Jinja2渲染的HTML模板，而模型(Model)可以使用其他库来实现(SQLAlchemy)。

## 2 Flask与HTTP

### 2.1 请求响应循环

- 客户端：用来提供给用户的与服务器通信的各种软件，这里指Web浏览器。
- 服务器端：为用户提供服务的服务器，也是我们的程序运行的地方。

- 当用户访问一个URL，浏览器便生成对应的HTTP请求，经由互联网发送到对应的Web服务器。Web服务器接收到请求，通过**WSGI**将HTTP格式的请求数据转换成我们的Flask程序能够使用的Python数据。在程序中，Flask根据请求的URL执行对应的视图函数，获取返回值生成响应。响应依次经过**WSGI**转换生成HTTP响应，再经由Web服务器传递，最终被发出请求的客户端接收。浏览器渲染响应中包含的HTML和CSS代码，并执行JavaScript代码，最终把解析后的页面呈现在用户浏览器的窗口中。

### 2.2 HTTP请求

URL是一个请求的起源。当我们输入指向服务器所在地址的URL，都会向服务器发送一个HTTP请求。一个标准的URL由很多部分组成，以下面这个URL为例：

```bash
http://helloflask.com/hello?name=Grey
```

| 信息             | 说明                                            |
| ---------------- | ----------------------------------------------- |
| http://          | 协议字符串，指定要使用的协议                    |
| helloflask.com   | 服务器的地址(域名)                              |
| /hello?name=Grey | 要获取的资源路径(path)，类似UNINX的文件目录结构 |



#### 2.2.1 请求报文

当我们在浏览器中访问这个URL时，随之产生的是一个发向http://helloflask.com所在服务器的请求。请求的实质是发送到服务器上的一些数据，这种浏览器与服务器之间交互的数据被称为报文(message)，请求时浏览器发送的数据被称为**请求报文(request message)**，而服务器返回的数据被称为**响应报文(response message)**。

- 请求报文由请求的方法、URL、协议版本、首部字段(header)以及内容实体组成。

  http://helloflask.com/hello?name=Grey的请求报文：

  | 组成说明                          | 请求报文内容         |
  | --------------------------------- | -------------------- |
  | 报文首部：请求行(方法、URL、协议) | GET /hello HTTP/1.1  |
  | 报文首部：各种首部字段            | Host: helloflask.com  Connection:keep-alive Cache-Control: max-age=0 User-Agent: Mozilla/5.0.... |
  | 空行                              |                      |
  | 报文主体                          | name=Grey            |

- 在Chrome浏览器中查看请求和响应报文

  。。。

- 报文由报文首部和报文主体组成，两者由空行分隔，请求报文的主体一般为空。如果URL中包含查询字符串，或是提交了表单，那么报文主体将会是查询字符串和表单数据。

- 常见的HTTP方法

  | 方法 | 说明     | 方法    | 说明           |
  | ---- | -------- | ------- | -------------- |
  | GET  | 获取资源 | DELETE  | 删除资源       |
  | POST | 传输数据 | HEAD    | 获得报文首部   |
  | PUT  | 传输文件 | OPTIONS | 询问支持的方法 |

  >HTTP中可用的首部字段列表可以在https://www.iana.org/assignments/message-headers/message-headers.xhtml看到。
  >
  >请求方法的详细列表和说明可以在RFC7231(https://tools.ietf.org/html/rfc7231)中看到。

  

#### 2.2.2 Rquest对象

Flask的请求对象request封装了从客户端发来的请求报文，我们能从它获取请求报文中的所有数据。

> 请求解析和响应封装实际上大部分是由Werkzeug完成的，Flask子类化Werkzeug的请求(Request)和响应(Response)对象并添加了和程序相关的特定功能。

- 假设请求的URL是http://helloflask.com/hello?name=Grey，当Flask接收到请求后，请求对象会提供多个属性来获取URL的各个部分。常用的属性：

  | 属性      | 值                        | 属性     | 值                                       |
  | --------- | ------------------------- | -------- | ---------------------------------------- |
  | path      | u'/hello'                 | base_url | u'http://helloflask.com/hello'           |
  | full_path | u'/hello?name=Grey'       | url      | u'http://helloflask.com/hello?name=Grey' |
  | host      | u'helloflask.com'         | url_root | u'http://helloflask.com/'                |
  | host_url  | u'http://helloflask.com/' |          |                                          |

  除了URL，请求报文中的其他信息都可以通过request对象提供的属性和方法获取，其中常用的部分：

  | 属性/方法                                                | 说明                                                         |
  | -------------------------------------------------------- | ------------------------------------------------------------ |
  | args                                                     | Werkzeug的ImmutableMultiDict对象。存储解析后的查询字符串，可通过字典方式获取键值。如果想获取未解析的原生查询字符串，可以使用query_string属性 |
  | blueprint                                                | 当前蓝本的名称                                               |
  | cookies                                                  | 一个包含所有随请求提交的cookies的字典                        |
  | data                                                     | 包含字符串形式的请求数据                                     |
  | endpoint                                                 | 与当前请求相匹配的端点值                                     |
  | files                                                    | Werkzeug的MultiDict对象，包含所有上传文件，可以使用字典的形式获取文件。使用的键为文件input标签中的name属性值，对应的值为Werkzeug的FileStorage对象，可以调用save()方法并传入保存路径来保存文件 |
  | form                                                     | Werkzeug的ImmutableMultiDict对象。与files类似，包含解析后的表单数据。表单字段值通过input标签的name属性值作为键获取 |
  | values                                                   | Werkzeug的CombinedMultiDict对象，结合了args和form属性的值    |
  | get_data(cache=True,as_text=False,parse_form_data=False) | 获取请求中的数据，默认读取为字节字符串(bytestring)，将as_text设为True，则返回值将是解码后的unicode字符串 |
  | get_json(self,force=False,silent=False,cache=True)       |                                                              |
  | headers                                                  |                                                              |
  | is_json                                                  |                                                              |
  | json                                                     |                                                              |
  | method                                                   | 请求的HTTP方法                                               |
  | referrer                                                 | 请求发起的源URL，即referer                                   |
  | scheme                                                   | 请求的URL模式(http或https)                                   |
  | user_agent                                               | 用户代理                                                     |

  > Werkzeug相关数据结构章节：http://werkzeug.pocoo.org/docs/latest/datastructures/。

```python
from flask import Flask,request

app = Flask(__name__)

@app.route('/hello')
def hello():
    name = request.args.get('name','Flask')
    return '<h1>Hello,%s!<h1>' % name
```



- 当我们从request对象的类型为MultiDicr或ImmutableMultiDict的属性(比如files、form、args)中直接使用键作为索引获取数据时(比如request.args['name'])，如果没有对应的键，那么会返回HTTP400错误响应(Bad Request，表示请求无效)，而不是抛出KeyError异常。
- 为了避免这个错误，我们应该使用get()方法获取数据，如果没有对应的值则返回None;get()方法的第二个参数可以设置默认值，比如request.args.get('name','Flask')。

#### 2.2.3 在Flask中处理请求

URL是指网络上资源的地址。在Flask中，我们需要让请求的URL匹配对应的视图函数，视图函数返回值就是URL对应的资源。

- 路由匹配

  为了便于将请求分发到对应的视图函数，程序实例中存储了一个路由表(app.url_map)，其中定义了URL规则和视图函数的映射关系。当请求发来后，Flask会根据请求报文中的URL(path部分)来尝试与这个表中所有URL规则进行匹配，调用匹配成功的视图函数。

  - 如果没有找到匹配的URL规则，说明程序中没有处理这个URL的视图函数，Flask会自动返回404错误响应(Not Found)。

  - 当匹配成功时，对应的视图函数就会被调用。

  - 使用flask routes命令可以查看程序中定义的所有路由，这个列表由app.url_map解析得到：

    | Endpoint | Methods | Rule                      |
    | -------- | ------- | ------------------------- |
    | hello    | Get     | /hello                    |
    | hi       | GET     | /goback/\<int:age\>       |
    | static   | GET     | /static/\<path:filename\> |

- 设置监听的HTTP方法

  ```python
  @app.route('/hello',methods=['GET','POST'])
  def hello():
      return '<h1>Hello, Flask!</h1>'
  ```

  

  - 当某个请求的方法不符合要求时，请求将无法被正常处理。Flask会自动返回一个405错误响应(Method Not Allowed，表示请求方法不允许)。

- URL处理

  /goback/\<int:age\>，表示为age变量添加了一个int转换器，Flask在解析这个URL变量时会将其转换为整型。URL中的变量部分默认类型为字符串，但Flask提供了一些转换器可以在URL规则里使用，如表所示:

  | 转换器 | 说明                         |
  | ------ | ---------------------------- |
  | string | 不包含斜线的字符串           |
  | int    | 整型                         |
  | float  | 浮点数                       |
  | path   | 包含斜线的字符串             |
  | any    | 匹配一系列给定值中的一个元素 |
  | uuid   | UUID字符串                   |

  

  。。。

  



#### 2.2.4 请求钩子





### 2.3 HTTP响应





### 2.4 Flask上下文

可以把编程中的上下文理解为当前环境(environmemt)的快照(snapshot)。Flask中有两种上下文，

- 程序上下文：存储了程序运行所必须的信息；
- 请求上下文：包含了请求的各种信息。

#### 2.4.1 上下文全局变量

每一个视图函数都需要上下文信息，

在前面的示例中，我们并没有传递这个参数，而是直接从Flask导入一个全局的request对象，然后在视图函数里直接调用request的属性获取数据。为什么在处理请求时，视图函数里的request就会自动包含对应请求的数据？这是因为Flask会在每个请求后自动激活当前请求的上下文，激活请求上下文后，request被临时设为全局可访问。而当每个请求结束后，Flask就销毁对应的请求上下文。



- request是动态的全局变量

- 在多线程服务器中，在同一个时间可能会有多个请求在处理。假设有三个客户端同时向服务器发送请求，这时每个请求都有各自不同的请求报文，所以请求对象也必然是不同的。因此，请求对象只在各自的线程内是全局的。Flask通过本地线程技术将请求对象在特定的线程和请求中全局可访问。

- 为了方便获取这两种上下文环境中存储的信息，Flask提供了四个上下文全局变量：

  | 变量名      | 上下文类别 | 说明                                                         |
  | ----------- | ---------- | ------------------------------------------------------------ |
  | current_app | 程序上下文 | 指向处理请求的当前程序实例说明                               |
  | g           | 程序上下文 | 替代Python的全局变量用法，确保仅在当前请求中可用。用于存储全局数据，每次请求都会重设 |
  | request     | 请求上下文 | 封装客户端的请求报文数据                                     |
  | session     | 请求上下文 | 用于记住请求之间的数据，通过签名的Cookie实现                 |

- **current_app**：为什么既然有了程序实例app对象，为什么还需要current_app变量。在不同的视图函数中，request对象都表示和视图函数对应的请求，也就是当前请求(current_request)。而程序也会有多个程序实例的情况，为了能获取对应的程序实例，而不是固定的某一个程序实例，就需要使用current_app变量。

- **g**：。。。。

#### 2.4.2 激活上下文

在下面这些情况下，Flask会自动帮我们激活程序上下文：

- 使用flask run命令启动程序时
- 使用旧的app.run()方法启动程序时
- 执行使用@app.cli.command()装饰器注册的flask命令时
- 使用flask shell命令启动Python Shell时

当请求进入时，Flask会自动激活请求上下文，这时我们可以使用request和session变量。另外，当请求上下文被激活时，程序上下文也被自动激活。当请求处理完毕后，请求上下文和程序上下文也会自动销毁。在请求处理时这两种=者拥有相同的生命周期。

。。。





### 2.5 HTTP进阶实践





## 3 模板





## 4 表单



## 5 数据库













## 12 自动化测试







## 13 性能测试





## 14 部署上线





## 15 Flask扩展开发





## 16 Flask工作原理与机制解析

