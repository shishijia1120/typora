# Flask项目实践







## 1 搭建环境

### 1.1 创建Git环境

- 创建Github仓库

  命名为flask-vuejs-madblog。在GitHub

- git clone

  克隆到本地，打开Git Bash:

  ```bash
  $ cd /d/python-code/
  $ git clone git@github.com:wangy8961/flask-vuejs-madblog.git
  ```

- init commit

  ```bash
  $ cd flask-vuejs-madblog/
  $ git status
  On branch master
  Your branch is up-to-date with 'origin/master'.
  Untracked files:
    (use "git add <file>..." to include in what will be committed)
  
          LICENSE
  
  nothing added to commit but untracked files present (use "git add" to track)
  $ git add .
  $ git commit -m "init"
  $ git push
  ```

- 分支管理

  ```bash
  $ git checkout -b dev
  $ git branch
  * dev
    master
  ```

### 1.2 配置Flask

```bash
> python -m venv venv
> venv\Scripts\activate
> pip install flask
> pip install freeze > requirements.txt
```



使用 `Visual Studio Code` 打开项目，提供 `.gitignore` 文件：

```bash
.idea/
__pycache__/
venv/
.env
app.db
madblog.log*
```

### 1.3  应用工厂

```python
from flask import Flask
from config import Config

def create_app(config_class=Config):
    app = Flask(__name__)
    app.config.from_object(config_class)
    
    # 注册blueprint
    from app.api import bp as api_bp
    app.register_blueprint(api_bp,url_prefix='/api')
    
    return app
```

### 1.4 API蓝图

创建app/api目录，然后新建api/\_\_init\_\_.py，定义蓝图：

```python
from flask import Blueprint

bp = Blueprint('api',__name__)

# 写在最后是为了防止循环导入，ping.py文件也会导入 bp
from app.api import ping
```



定义第一个路由函数，当客户端访问/ping时返回Pong!时的JSON数据，创建api/ping.py:

```python
from flask import jsonify
from app.api import bp

@bp.route('/ping',methods=['GET'])
def ping():
    '''前端Vue.js用来测试与后端Flask API的连通性'''
    return jsonify('Pong!')


```

### 1.5 应用启动文件

创建`back-end/madblog.py`：

```python
from app import create_app

app = create_app()
```



### 1.6 配置文件

创建 `back-end/config.py`：

```python
import os
from dotenv import load_dotenv

basedir = os.path.abspath(os.path.dirname(__file__))
load_dotenv(os.path.join(basedir, '.env'), encoding='utf-8')


class Config(object):
    pass
```



我们的 `Flask` 应用会使用很多系统环境变量，准备写在 `back-end/.env` 中，Python 可以使用 `python-dotenv` 这个包来读取环境变量信息，所以我们先安装这个包：

```bash
> pip install python-dotenv
> pip freeze > requirements.txt
```

创建 `back-end/.env`：

```bash
FLASK_APP=madblog.py
FLASK_DEBUG=1
```

### 1.7 启动应用

```bash
> flask run
```



### 1.8 提交代码

```bash
$ git add .
$ git commit -m "1. 后端Flask RESTful API提供测试接口 Ping-Pong!"
$ git checkout master
$ git merge dev
$ git branch -d dev
$ git branch
```

将本地 `master` 分支代码上传到 Github 代码仓库中的 `master` 分支：

```bash
$ git push
或者：
$ git push -u origin master
```

打上标签 `tag`并上传：

```bash
$ git tag v0.1
$ git tag
v0.1
$ git show v0.1

1. 同步单个标签
$ git push origin v0.1

2. 同步所有标签
$ git push --tags
或者：
$ git push origin --tags
```



## 2 创建用户相关的API

### 2.1 拉去最新代码



### 2.2 ORM: SQLAlchemy

- 安装 `Flask-SQLAlchemy` 插件，还有数据表结构有变化后进行迁移的 `Flask-Migrate` 插件

  ```python
  (venv) D:\python-code\flask-vuejs-madblog\back-end>pip install flask-sqlalchemy flask-migrate
  (venv) D:\python-code\flask-vuejs-madblog\back-end>pip freeze > requirements.txt
  ```

- 修改配置文件 `back-end/config.py`，默认使用 `SQLite` 数据库：

  ```python
  import os
  from dotenv import load_dotenv
  
  basedir = os.path.abspath(os.path.dirname(__file__))
  load_dotenv(os.path.join(basedir, '.env'))
  
  
  class Config(object):
      SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \
          'sqlite:///' + os.path.join(basedir, 'app.db')
      SQLALCHEMY_TRACK_MODIFICATIONS = False
  ```

- 修改 `app/__init__.py`，引入并初始化插件：

  ```python
  from flask import Flask
  from flask_cors import CORS
  from flask_sqlalchemy import SQLAlchemy
  from flask_migrate import Migrate
  from config import Config
  
  
  # Flask-SQLAlchemy plugin
  db = SQLAlchemy()
  # Flask-Migrate plugin
  migrate = Migrate()
  
  
  def create_app(config_class=Config):
      app = Flask(__name__)
      app.config.from_object(config_class)
  
      # Enable CORS
      CORS(app)
      # Init Flask-SQLAlchemy
      db.init_app(app)
      # Init Flask-Migrate
      migrate.init_app(app, db)
  
      # 注册 blueprint
      from app.api import bp as api_bp
      app.register_blueprint(api_bp, url_prefix='/api')
  
      return app
  ```

  

### 2.3 定义User用户数据模型

```python
from app import db


class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(64), index=True, unique=True)
    email = db.Column(db.String(120), index=True, unique=True)
    password_hash = db.Column(db.String(128))  # 不保存原始密码

    def __repr__(self):
        return '<User {}>'.format(self.username)
```

修改 `app/__init__.py`，在文件末尾添加：

```bash
from app import models
```

### 2.4 第一次数据库迁移

- 创建迁移数据库

  ```bash
  flask db init
  ```

- 生成迁移脚本

  ```bash
  flask db migrate -m "add users table"
  ```

- 将迁移脚本应用到数据库中

  ```python
  flask db upgrade
  ```

  

### 2.5 存储用户密码的hash值









## 3 个人主页与用户头像

用户认证修改为 JWT（JSON Web Token），后端使用 pyjwt 库生成 JWT 并验证合法性；前端使用 JSON.parse 解析 JWT 中的 payload 数据。提示消息改用 vue-toasted 插件，方便好用。前端每次访问 API 都需要附带 Token 到 Authorization 请求头中，使用请求拦截器自动添加进去，另外设置了响应拦截器，自动处理 401 和 404 错误。用户头像使用在线的 Gravatar 服务。

### 3.1 JWT

```bash
> pip install pyjwt
> pip freeze > requirements.txt
```

修改 User 数据模型 `back-end/app/models.py`，删除之前的 `token`和 `token_expiration` 字段：

```python
class User(PaginatedAPIMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(64), index=True, unique=True)
    email = db.Column(db.String(120), index=True, unique=True)
    password_hash = db.Column(db.String(128))  # 不保存原始密码

    ...
    def get_jwt(self, expires_in=600):
        now = datetime.utcnow()
        payload = {
            'user_id': self.id,
            'name': self.name if self.name else self.username,
            'exp': now + timedelta(seconds=expires_in),
            'iat': now
        }
        return jwt.encode(
            payload,
            current_app.config['SECRET_KEY'],
            algorithm='HS256').decode('utf-8')

    @staticmethod
    def verify_jwt(token):
        try:
            payload = jwt.decode(
                token,
                current_app.config['SECRET_KEY'],
                algorithms=['HS256'])
        except (jwt.exceptions.ExpiredSignatureError, jwt.exceptions.InvalidSignatureError) as e:
            # Token过期，或被人修改，那么签名验证也会失败
            return None
        return User.query.get(payload.get('user_id'))
```

**在配置文件 `back-end/config.py` 中添加配置项: `SECRET_KEY = os.environ.get('SECRET_KEY') or 'you-will-never-guess'`**

**在配置文件 `back-end/config.py` 中添加配置项: `SECRET_KEY = os.environ.get('SECRET_KEY') or 'you-will-never-guess'`**

修改 `back-end/app/api/tokens.py`：

```python
from flask import jsonify, g
from app import db
from app.api import bp
from app.api.auth import basic_auth


@bp.route('/tokens', methods=['POST'])
@basic_auth.login_required
def get_token():
    token = g.current_user.get_jwt()
    db.session.commit()
    return jsonify({'token': token})
```

> 注意： `JWT` 没办法回收（不需要 `DELETE /tokens`），只能等它过期，所以有效时间别设置太长



### 3.2 Flask-Migrate迁移

修改 User 数据模型后要进行数据库迁移：





### 3.3 个人主页

后端给 User 数据模型增加一些字段（注意： 修改数据模型后请自行用 Flask-Migrate 进行数据库迁移），并修改 `to_dict()` 方法：

```python
class User(PaginatedAPIMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(64), index=True, unique=True)
    email = db.Column(db.String(120), index=True, unique=True)
    password_hash = db.Column(db.String(128))  # 不保存原始密码
    name = db.Column(db.String(64))
    location = db.Column(db.String(64))
    about_me = db.Column(db.Text())
    member_since = db.Column(db.DateTime(), default=datetime.utcnow)
    last_seen = db.Column(db.DateTime(), default=datetime.utcnow)

    ...
    def to_dict(self, include_email=False):
        data = {
            'id': self.id,
            'username': self.username,
            'name': self.name,
            'location': self.location,
            'about_me': self.about_me,
            'member_since': self.member_since.isoformat() + 'Z',
            'last_seen': self.last_seen.isoformat() + 'Z',
            '_links': {
                'self': url_for('api.get_user', id=self.id),
                'avatar': self.avatar(128)
            }
        }
        if include_email:
            data['email'] = self.email
        return data
```





### 



### 3.4 提交代码

```
$ git add .
$ git commit -m "5. 个人主页与用户头像"
$ git checkout master
$ git merge dev
$ git branch -d dev
```

将本地 `master` 分支代码上传到 Github 代码仓库中的 `master` 分支：

```
$ git push -u origin master
```

打上标签 `tag`并上传：

```
$ git tag v0.5
$ git push origin v0.5
```





## 4 个人博客

### 4.1 声明模型

修改 `back-end/app/models.py`，增加 Post 数据模型：

```python
class Post(PaginatedAPIMixin, db.Model):
    __tablename__ = 'posts'
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(255))
    summary = db.Column(db.Text)
    body = db.Column(db.Text)
    timestamp = db.Column(db.DateTime, index=True, default=datetime.utcnow)
    views = db.Column(db.Integer, default=0)

    def __repr__(self):
        return '<Post {}>'.format(self.title)
```



### 4.2 一对多关系

每个用户可以发布多篇博客，User 与 Post 是一对多关系，示意图如下：

```python
class User(PaginatedAPIMixin, db.Model):
    ...
    # 反向引用，直接查询出当前用户的所有博客文章; 同时，Post实例中会有 author 属性
    # cascade 用于级联删除，当删除user时，该user下面的所有posts都会被级联删除
    posts = db.relationship('Post', backref='author', lazy='dynamic',
                            cascade='all, delete-orphan')
```

在 Post 类中增加：

```python
class Post(PaginatedAPIMixin, db.Model):
    ...
    # 外键, 直接操纵数据库当user下面有posts时不允许删除user，下面仅仅是 ORM-level “delete” cascade
    # db.ForeignKey('users.id', ondelete='CASCADE') 会同时在数据库中指定 FOREIGN KEY level “ON DELETE” cascade
    author_id = db.Column(db.Integer, db.ForeignKey('users.id'))
```

迁移数据库：

```bash
flask db migrate -m "add posts table"
flask db upgrade
```

- 参数说明：

  - `db.relationship()` 让 `posts` 属性指向 Post 类并加载多篇博客，实现一对多关系；更多关系 API 请参考： https://docs.sqlalchemy.org/en/latest/orm/relationship_api.html

    `backref='author'` 会在 Post 类上声明一个新的属性 `author`，即可以使用 `post.author` 来获取该博客的作者对象

    `lazy` 决定了 `SQLAlchemy` 什么时候从数据库中加载数据：

    - `select`： （默认值）`SQLAlchemy` 会使用一个标准的 `select`语句必要时一次性加载数据，即 `user.posts` 会直接返回包含该用户的所有博客对象的`列表`

    - `joined`： 告诉 `SQLAlchemy` 使用 `JOIN` 语句作为父级在同一查询中来加载关系
    - `subquery`： 类似 `joined` ，但是 `SQLAlchemy` 会使用子查询
    - `dynamic`： 在有多条数据的时候是特别有用，它不是直接加载这些数据，`SQLAlchemy` 会返回一个查询对象 `BaseQuery`，在加载数据前您可以过滤（提取）它们

  - `cascade='all, delete-orphan'` 用于级联删除，当删除user时，该user下面的所有posts都会被级联删除，详情参考： https://docs.sqlalchemy.org/en/latest/orm/cascades.html

### 4.3 RESTful API设计

| HTTP方法 | 资源URL           | 说明               |
| :------- | :---------------- | :----------------- |
| `GET`    | `/api/posts`      | 返回所有文章的集合 |
| `POST`   | `/api/posts`      | 添加一篇新文章     |
| `GET`    | `/api/posts/<id>` | 返回一篇文章       |
| `PUT`    | `/api/posts/<id>` | 修改一篇文章       |
| `DELETE` | `/api/posts/<id>` | 删除一篇文章       |

创建 `app/api/posts.py`：

```python
from flask import request, jsonify, url_for, g
from app.api import bp
from app.api.auth import token_auth
from app.api.errors import error_response, bad_request
from app.extensions import db
from app.models import Post


@bp.route('/posts', methods=['POST'])
@token_auth.login_required
def create_post():
    '''添加一篇新文章'''
    pass

@bp.route('/posts', methods=['GET'])
def get_posts():
    '''返回文章集合，分页'''
    pass

@bp.route('/posts/<int:id>', methods=['GET'])
def get_post(id):
    '''返回一篇文章'''
    pass

@bp.route('/posts/<int:id>', methods=['PUT'])
@token_auth.login_required
def update_post(id):
    '''修改一篇文章'''
    pass

@bp.route('/posts/<int:id>', methods=['DELETE'])
@token_auth.login_required
def delete_post(id):
    '''删除一篇文章'''
    pass
```

#### 4.3.1 添加文章

只有通过 `Token` 认证的用户才能发表文章：

```python
@bp.route('/posts', methods=['POST'])
@token_auth.login_required
def create_post():
    '''添加一篇新文章'''
    data = request.get_json()
    if not data:
        return bad_request('You must post JSON data.')
    message = {}
    if 'title' not in data or not data.get('title'):
        message['title'] = 'Title is required.'
    elif len(data.get('title')) > 255:
        message['title'] = 'Title must less than 255 characters.'
    if 'body' not in data or not data.get('body'):
        message['body'] = 'Body is required.'
    if message:
        return bad_request(message)

    post = Post()
    post.from_dict(data)
    post.author = g.current_user  # 通过 auth.py 中 verify_token() 传递过来的（同一个request中，需要先进行 Token 认证）
    db.session.add(post)
    db.session.commit()
    response = jsonify(post.to_dict())
    response.status_code = 201
    # HTTP协议要求201响应包含一个值为新资源URL的Location头部
    response.headers['Location'] = url_for('api.get_post', id=post.id)
    return response
```

#### 4.3.2 修改文章

用户首先必须通过 `Token` 认证，而且他还必须是该博客文章的作者才允许修改：

```python
@bp.route('/posts/<int:id>', methods=['PUT'])
@token_auth.login_required
def update_post(id):
    '''修改一篇文章'''
    post = Post.query.get_or_404(id)
    if g.current_user != post.author:
        return error_response(403)

    data = request.get_json()
    if not data:
        return bad_request('You must post JSON data.')
    message = {}
    if 'title' not in data or not data.get('title'):
        message['title'] = 'Title is required.'
    elif len(data.get('title')) > 255:
        message['title'] = 'Title must less than 255 characters.'
    if 'body' not in data or not data.get('body'):
        message['body'] = 'Body is required.'
    if message:
        return bad_request(message)

    post.from_dict(data)
    db.session.commit()
    return jsonify(post.to_dict())
```

#### 4.3.3 返回一篇文章

不需要认证，游客也能查看文章详情：

```python
@bp.route('/posts/<int:id>', methods=['GET'])
def get_post(id):
    '''返回一篇文章'''
    post = Post.query.get_or_404(id)
    post.views += 1
    db.session.add(post)
    db.session.commit()
    return jsonify(post.to_dict())
```

#### 4.3.4 返回文章列表

不需要认证，游客也能访问前端首页，显示博客列表：

```python
@bp.route('/posts', methods=['GET'])
def get_posts():
    '''返回文章集合，分页'''
    page = request.args.get('page', 1, type=int)
    per_page = min(request.args.get('per_page', 10, type=int), 100)
    data = Post.to_collection_dict(Post.query.order_by(Post.timestamp.desc()), page, per_page, 'api.get_posts')
    return jsonify(data)
```

如果请求的查询参数 `page` 或 `per_page` 没有对应的结果集，`Post.to_collection_dict()` 方法会自动抛出 `404` 错误

#### 4.3.5 删除文章

用户首先必须通过 `Token` 认证，而且他还必须是该博客文章的作者才允许删除：

```python
@bp.route('/posts/<int:id>', methods=['DELETE'])
@token_auth.login_required
def delete_post(id):
    '''删除一篇文章'''
    post = Post.query.get_or_404(id)
    if g.current_user != post.author:
        return error_response(403)
    db.session.delete(post)
    db.session.commit()
    return '', 204
```

### 4.4  提交代码

```bash
$ git add .
$ git commit -m "6. 博客文章CURD与Markdown"
$ git checkout master
$ git merge dev
$ git branch -d dev
```

将本地 `master` 分支代码上传到 Github 代码仓库中的 `master` 分支：

```bash
$ git push -u origin master
```

打上标签 `tag`并上传：

```bash
$ git tag v0.6
$ git push origin v0.6
```

## 5 粉丝关注

### 5.1 一对多关系





### 5.2 多对一关系





### 5.3 一对一关系







### 5.4  多对多关系



### 5.5 粉丝机制

修改 `back-end/app/models.py`：

```python
followers = db.Table(
    'followers',
    db.Column('follower_id', db.Integer, db.ForeignKey('users.id')),
    db.Column('followed_id', db.Integer, db.ForeignKey('users.id')),
    db.Column('timestamp', db.DateTime, default=datetime.utcnow)
)

class User(PaginatedAPIMixin, db.Model):
    ...
    # followeds 是该用户关注了哪些用户列表
    # followers 是该用户的粉丝列表
    followeds = db.relationship(
        'User', secondary=followers,
        primaryjoin=(followers.c.follower_id == id),
        secondaryjoin=(followers.c.followed_id == id),
        backref=db.backref('followers', lazy='dynamic'), lazy='dynamic')




```















## 6 单元测试

未经测试的小猫，肯定不是一只好猫。本文是填补之前没有进行任何单元测试的坑，使用 Python 自带的 unittest 包，当然你也可以使用 pytest 包。另外，Flask 内建了一个测试客户端 app.test_client()，它能复现程序运行在 Web 服务器中的环境，扮演成客户端从而发送请求。为了查看我们的测试代码覆盖率，需要安装 coverage 包，并创建一个 Flask CLI 命令 - flask test，以后修改业务逻辑了，请跑一遍测试用例

### 6.1 测试框架unittest

使用 Python 自带的 `unittest` 包来测试我们的 Flask 应用程序，创建 `back-end/tests` 目录，并新建 `back-end/tests/__init__.py`：

```python
from config import Config


class TestConfig(Config):
    TESTING = True
    SQLALCHEMY_DATABASE_URI = 'sqlite://'
```

然后创建我们第一个单元测试 `back-end/tests/test_basic.py`，以 `test` 开头的文件可以被 `unittest` 找到：

```python
import unittest
from flask import current_app
from app import create_app, db
from tests import TestConfig


class BasicsTestCase(unittest.TestCase):
    def setUp(self):
        '''每个测试之前执行'''
        self.app = create_app(TestConfig)  # 创建Flask应用
        self.app_context = self.app.app_context()  # 激活(或推送)Flask应用上下文
        self.app_context.push()
        db.create_all()  # 通过SQLAlchemy来使用SQLite内存数据库，db.create_all()快速创建所有的数据库表

    def tearDown(self):
        '''每个测试之后执行'''
        db.session.remove()
        db.drop_all()  # 删除所有数据库表
        self.app_context.pop()  # 退出Flask应用上下文

    def test_app_exists(self):
        self.assertFalse(current_app is None)

    def test_app_is_testing(self):
        self.assertTrue(current_app.config['TESTING'])
```

>**注意： 我们的调试函数都是以 `test` 开头，这样 `unittest` 就会将这些函数自动识别为 `测试函数`，并运行它们**

