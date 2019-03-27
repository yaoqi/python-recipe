flask是一个Python语言开发的web“微框架”，和django不同的是，它既没有数据库、也没有表单验证等工具它本身仅仅提供了一个WSGI的桥梁，其他东西统统靠你来定制，具有很大的灵活性

本文简要地阐述了flask本身的核心知识（不涉及任何扩展）

想快速上手的也可直接看[官方文档](http://flask.pocoo.org/docs/1.0/quickstart/)

<!--more-->

## app实例

创建app实例

```python
from flask import Flask
app = Flask(__name__)
```

### 配置

建议把配置分成3类：开发配置、测试配置和生产配置，方便随时切换

以下就是config.py

```python
import os
import sys

basedir = os.path.abspath(os.path.dirname(os.path.dirname(__file__)))
WIN = sys.platform.startswith('win')
prefix = 'sqlite:///' if WIN else 'sqlite:////'


class BaseConfig:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'dev key'
    ...


class DevelopmentConfig(BaseConfig):
    SQLALCHEMY_DATABASE_URI = prefix + os.path.join(basedir, 'data-dev.db')


class TestingConfig(BaseConfig):
    TESTING = True
    WTF_CSRF_ENABLED = False
    SQLALCHEMY_DATABASE_URI = 'sqlite:///:memory:'


class ProductionConfig(BaseConfig):
    SQLALCHEMY_DATABASE_URI = os.getenv('DATABASE_URL', prefix + os.path.join(basedir, 'data.db'))


config = {
    'development': DevelopmentConfig,
    'testing': TestingConfig,
    'production': ProductionConfig
}

```

以对象实例的方式导入app

```python
import os
from app.config import config

config_name = os.getenv('FLASK_CONFIG', 'development')
app.config.from_object(config[config_name])
```

## 路由

路由是将特定的业务代码（即视图函数）绑定到某个url上，以实现某个功能

### 注册

flask中使用装饰器来注册路由

```python
@app.route('/')
def index():
    return 'Hello world.'
```

可以为路由传递变量，变量左边可以带转换器用来转换变量的类型

```python
@app.route('/user/<string:username>')
def user_profile(username):
    return f'User {username}'
```

常用的转换器有6种：string, int, float, path, any, uuid

比较特殊的是any，格式如下（var变量只接受a, b其中的任意一值）

``` python
@app.route('/<any(a, b):var>/')
```

### 构造url

使用url\_for函数可以反向构建访问路由的url

```python
url_for('index')  # '/'
url_for('user_profile', username='alphardex')  # '/user/alphardex'
url_for('index', _external=True)  # 'http://localhost:5000/' 绝对路径
```

### HTTP

路由默认支持GET方法，如果需要POST方法则需在route的methods中传入

HTTP methods的用处如下：

- GET：获取资源
- POST：创建资源
- PUT：更新资源
- DELETE：删除资源

```python
@app.route('/login', methods=['GET', 'POST'])
```

如果想更改HTTP请求头内容，那就要用到make\_response

比如制作网站的RSS，就需要把响应的mimetype设置为application/xml

```python
@app.route('/rss')
def rss():
    articles = Article.query.order_by(db.desc('date')).limit(10)
    rss = render_template('rss.xml', articles=articles)
    response = make_response(rss)
    response.mimetype = 'application/xml'
    return response
```

## 命令

运行命令前必须设置好环境变量（比如FLASK_APP)

推荐用python-dotenv将环境变量以.flaskenv和.env（敏感信息）形式保存到本地

运行flask

```bash
$ flask run
```

运行包含app上下文的shell

```bash
$ flask shell
```

自定义命令（更多请参考[click文档](http://click.pocoo.org/)）

```python
@app.cli.command()
@click.option('--username', help='user that you greet', default='alphardex')
def greet(username):
    """Greet someone."""
    click.echo(f'Hello, {username}!')
```

## 模板

渲染一个模板，简言之就是通过变量来生成HTML

```python
from flask import render_template

@app.route('/')
def index():
    greetings = 'Hello world.'
    return render_template('index.html', greetings=greetings)
```

模板存放在templates文件夹中，比如上面的index.html

```html
<!doctype html>
<h1>{{ greetings }}</h1>
```

通过mustache语法将变量传入模板并渲染，同时也支持if、for等控制流语句语法，更高级的有过滤器、模板继承、宏等

过滤器的添加格式如下所示

```html
{{ var|filter }}
```

提几个常用的过滤器：

* safe: 避免HTML的自动转义，本质上是个Markup对象
* length: 获取变量长度
* default: 为变量设置默认值
* trim: 去除变量前后的空格
* tojson: 将变量转化为json
* truncate: 截断字符串，常用于显示文章摘要

模板语法具体可看[jinja2文档](http://jinja.pocoo.org/docs/2.10/templates/)

网站的静态文件放在static文件夹中，通过反向构造url访问

```python
url_for('static', filename='style.css')
```

## 上下文全局变量

* current_app：指向处理请求的app实例
* g：global的简写，以object的方式存储信息（比如用户登录后的用户对象 g.user）
* request：以dict形式存储HTTP请求相关变量
* session：以dict的方式存储会话信息（比如用户登录后的用户id session['user_id']）

以下是request所封装的几个最常用的参数，全部参数请点[这里](http://flask.pocoo.org/docs/1.0/api/?highlight=args#incoming-request-data)

```python
request.args        # GET请求的查询字符串
request.cookies     # cookies信息
request.files       # 请求上传的文件
request.form        # POST请求的表单数据
request.headers     # 请求头
request.method      # 请求类型
request.path        # 请求路径
request.referrer    # 请求发源地址
request.remote_addr # 用户的ip地址
request.get_json()  # 获取api的json数据
```

## 工具函数

* abort：放弃请求
* flash：闪现信息，可以附带类别
* jsonify：将数据序列化为json，常用于设计restful api
* redirect：重定向

## 工厂模式

工厂模式使得app在创建之时能同时完成以下步骤：加载配置，初始化扩展，注册蓝本，注册shell上下文，以及注册错误处理函数等

```python
def create_app(config_name=None):
    if config_name is None:
        config_name = os.getenv('FLASK_CONFIG', 'development')

    app = Flask(__name__)
    app.config.from_object(config[config_name])

    register_blueprints(app)
    register_extensions(app)
    register_shell_context(app)
    register_errors(app)

    return app

def register_extensions(app):
    debugtoolbar.init_app(app)
    db.init_app(app)
    ...

def register_blueprints(app):
    app.register_blueprint(main_bp)
    app.register_blueprint(auth_bp, url_prefix='/auth')
    app.register_blueprint(user_bp, url_prefix='/user')

def register_shell_context(app):
    @app.shell_context_processor
    def make_shell_context():
        return {'db': db, 'User': User}

def register_errors(app):
    @app.errorhandler(400)
    def bad_request(e):
        return render_template('errors/400.html'), 400
    ...
```

## 蓝本

用来实现模块化编程

例如一个app（名字叫flasky）通常会有以下的文件夹结构

```powershell
├─flasky
│  ├─blueprints
│  ├─static
│  │  └─css
│  └─templates
│      ├─auth
│      ├─errors
│      ├─main
│      └─user
└─tests
```

其中blueprints就是蓝本文件夹，里面存放着和templates对应的4个蓝本

```powershell
├─blueprints
│      auth.py
│      main.py
│      user.py
│      __init__.py
```

这4个蓝本中\_\_init\_\_.py负责Python的包结构，其余三个则是app的3个功能模块：认证、主页、用户

以auth.py为例

```python
from flask import Blueprint, ...
...(此处省略了一堆import)

bp = Blueprint('auth', __name__)

@bp.route('/login', methods=['GET', 'POST'])
def login():
    ...

@bp.route('/register', methods=['GET', 'POST'])
def register():
    ...

@bp.route('/logout')
def log_out():
    ...
```

蓝本也可以注册路由，如果反向构造url的话就得加上蓝本名，比如url\_for('auth.login')

通过上文便可得知，蓝本的注册应在工厂函数中执行，并且每个蓝本可以通过url\_prefix来给url添加前缀

最后别忘记在工厂函数中用register\_blueprint注册一下

## 脚手架

为了迅速搭建一个像样的flask网站，我们可以使用脚手架

之前看到PegasusWang推荐的cookiecutter-flask，是个不错的选择，但是新手可能会看不懂里面代码是如何封装的

于是本人做出了一个更user-friendly的脚手架——[cookiecutter-flask-bootstrap](https://github.com/alphardex/cookiecutter-flask-bootstrap)

这个脚手架的功能大致和上个脚手架差不多，不过更加轻量化，而且结构更加清晰明了，best practice也基本都做到了

希望大家用的开心

最后还要感谢李辉大大的狼书，给了我很大的帮助

## 高级玩法

flask是基于werkzeug实现的，因此有些高级的玩法也得借助它完成

### 自定义路由转换器

我们都知道flask的路由映射是存储在app.url\_map中的，因此查阅官方文档[有关url\_map的部分](http://flask.pocoo.org/docs/1.0/api/?highlight=url_map#flask.Flask.url_map)，就能轻松实现

``` python
from flask import Flask
from urllib.parse import unquote

class ListConverter(BaseConverter):
    def __init__(self, url_map, separator='+'):
        super().__init__(url_map)
        self.separator = unquote(separator)

    def to_python(self, value): # 把路径转换成一个Python对象，比如['python', 'javascript', 'sql']
        return value.split(self.separator)

    def to_url(self, values): # 把参数转换成符合URL的形式，比如/python+javascript+sql/
        return self.separator.join(BaseConverter.to_url(self, value) for value in values)

app = Flask(__name__)
app.url_map.converters['list'] = ListConverter

@app.route('/list1/<list:var>/')
def list1(var):
    return f'Separator: + {var}'


@app.route('/list2/<list(separator=u"|"):var>/')
def list2(var):
    return f'Separator: | {var}'
```

官方文档仅仅用了逗号分隔符，而在这里我们通过添加了separator属性来实现了自定义分隔符
访问如下链接体验下效果

```
http://localhost:9000/list1/python+javascript+sql
http://localhost:9000/list2/python|javascript|sql
```

### 强制响应格式为json

API返回的一般都是json，故在每个视图函数中调用jsonify将dict序列化为json

``` python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def index():
    return jsonify({'message': 'Hello World!'})

@app.route('/foo')
def foo():
    return jsonify({'message': 'Hello foo!'})
```

但其实没必要这么做，因为flask的Response是可以定制的

flask的app实例提供了response\_class属性，默认是Response

继续查照定义，发现Response其实继承了werkzeug里的[BaseResponse](http://werkzeug.pocoo.org/docs/0.14/wrappers/#werkzeug.wrappers.BaseResponse)

通过查阅BaseResponse，我们可以重载Response的force\_type类方法，将类型为dict的response直接jsonify，并且无需在每个视图函数中都显式调用jsonify函数了

``` python
from flask import Flask, jsonify
from werkzeug.wrappers import Response

class JSONResponse(Response):
    @classmethod
    def force_type(cls, response, environ=None):
        if isinstance(response, dict):
            response = jsonify(response)
        return super().force_type(response, environ)

app = Flask(__name__)
app.response_class = JSONResponse

@app.route('/')
def index():
    return {'message': 'Hello World!'}

@app.route('/foo')
def foo():
    return {'message': 'Hello foo!'}
```

### 用方法视图实现Restful API

flask可以说是写Restful API的利器

虽说诸如flask-restful之类扩展为我们提供了便利，但是用flask提供的[MethodView](http://flask.pocoo.org/docs/1.0/views/#method-views-for-apis)写API同样也很方便

``` python
from flask import Flask, jsonify
from flask.views import MethodView

class ItemsAPI(MethodView):

    def get(self):
        # get todo items

    def post(self):
        # create a todo item

class ItemAPI(MethodView):

    def get(self, item_id):
        # get a todo item

    def put(self, item_id):
        # modify a todo item

    def patch(self, item_id):
        # toggle a todo item

    def delete(self, item_id):
        # delete a todo item

app = Flask(__name__)
bp.add_url_rule('/todo/items', view_func=ItemsAPI.as_view('items'), methods=['GET', 'POST'])
app.add_url_rule('/todo/items/<int:item_id>', view_func=ItemAPI.as_view('item'), methods=['GET', 'PUT', 'PATCH', 'DELETE'])
```

以上就可以实现一个简单的TODO List服务

### 用ptpython替换默认的shell

[ptpython](https://github.com/prompt-toolkit/ptpython)是一个支持代码高亮和自动补全的repl，本人的最爱。把它集成到cli上也挺简单的，关键是添加上下文

``` python
from flask.cli import with_appcontext

def register_cli(app):
    ...

    @app.cli.command()
    @with_appcontext
    def ptshell():
        """Use ptpython as shell."""
        try:
            from ptpython.repl import embed
            embed(app.make_shell_context())
        except ImportError:
            click.echo('ptpython not installed! Use the default shell instead.')
```
