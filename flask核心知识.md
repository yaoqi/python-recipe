flask是一个Python语言开发的web“微框架”，和django不同的是，它既没有数据库、也没有表单验证等工具它本身仅仅提供了一个WSGI的桥梁，其他东西统统靠你来定制，具有很大的灵活性

## 脚手架

为了迅速搭建一个像样的flask网站，我们可以使用脚手架

之前在Github上看到cookiecutter-flask，是个不错的选择，但是新手可能会看不懂里面代码是如何封装的

于是本人做出了一个更user-friendly的脚手架——[cookiecutter-flask-bootstrap](https://github.com/alphardex/cookiecutter-flask-bootstrap)

这个脚手架的功能大致和上个脚手架差不多，不过更加轻量化，而且结构更加清晰明了，best practice也基本都做到了，希望大家用的开心:d

最后还要感谢李辉大大的[狼书](https://book.douban.com/subject/30310340/)，给了我很大的帮助

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

如果想通过路由直接访问文件呢？用path转换器和send\_from\_directory就行了

``` python
@app.route('/uploads/<path:filename>')
def get_image(filename):
    return send_from_directory(current_app.config['UPLOAD_PATH'], filename)
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
    articles = Article.query.order_by(Article.date.desc).limit(10)
    rss = render_template('rss.xml', articles=articles)
    response = make_response(rss)
    response.mimetype = 'application/xml'
    return response
```

## 模板

渲染一个模板，简言之就是通过上下文变量来生成HTML

```python
from flask import render_template

@app.route('/')
def index():
    greetings = 'Hello world.'
    return render_template('index.html', greetings=greetings)
```

render\_template中第一个参数是要渲染的模板文件名，**其余参数则是上下文变量**

通过mustache语法将上下文变量传入模板并渲染，同时也支持if、for等控制流语句语法，更高级的有过滤器、模板继承、宏等

提几个常用的过滤器：

- safe: 避免HTML的自动转义，本质上是个Markup对象
- length: 获取变量长度
- default: 为变量设置默认值
- trim: 去除变量前后的空格
- tojson: 将变量转化为json
- truncate: 截断字符串，常用于显示文章摘要

网站的静态文件放在static文件夹中，通过反向构造url访问

```python
url_for('static', filename='style.css')
```

## 上下文全局变量

- current_app：指向处理请求的app实例
- g：global的简写，以object的方式存储信息（比如用户登录后的用户对象 g.user）
- request：以dict形式存储HTTP请求相关变量
- session：以dict的方式存储会话信息（比如用户登录后的用户id session['user_id']）

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

- abort：放弃请求
- flash：闪现信息，可以附带类别
- jsonify：将数据序列化为json，常用于设计restful api
- redirect：重定向

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
    ...

def register_blueprints(app):
    app.register_blueprint(main_bp)
    ...

def register_shell_context(app):
    @app.shell_context_processor
    def make_shell_context():
        return {'db': db, ...}

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

蓝本的注册应在工厂函数中执行，并且每个蓝本可以通过url\_prefix来给url添加前缀

## 常用插件

- flask-admin：提供admin管理后台
- flask-avatars：生成用户头像
- flask-ckeditor：集成富文本编辑器ekeditor
- flask-cors：提供跨域支持
- flask-dropzone：集成文件上传插件dropzone
- flask-login：处理用户登陆认证逻辑
- flask-mail：邮箱服务
- flask-migrate：提供数据库迁移支持
- flask-moment：提供时间规范化支持
- flask-mongoengine：集成mongoengine——面向mongodb的ORM
- flask-restful：RESTful API支持
- flask-socketio：集成socketio，常用于编写聊天室
- flask-sqlalchemy：集成sqlalchemy——面向标准SQL的ORM
- flask-weasyprint：提供PDF打印功能
- flask-whooshee：集成whooshee——全文搜索引擎
- flask-wtf：集成wtforms——表单支持
- bootstrap-flask：集成bootstrap，并提供一些有用的宏
- faker：能生成假数据，用于测试

## 高级玩法

### 强制响应格式

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

但其实没必要这么做，因为**flask的Response是可以定制的**

flask的app实例提供了response\_class属性，默认是Response

继续查照定义，发现Response其实继承了werkzeug里的[BaseResponse](http://werkzeug.pocoo.org/docs/0.14/wrappers/#werkzeug.wrappers.BaseResponse)

通过查阅BaseResponse，我们可以重载Response的force\_type类方法，将类型为dict的response直接jsonify，并且无需在每个视图函数中都显式调用jsonify函数了

``` python
from flask import Flask, jsonify, Response

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

当然，你也可以类似地强制响应格式为xml，[RSSHub](https://github.com/alphardex/RSSHub-python)就是这么实现的

``` python
from flask import Flask, Response

class XMLResponse(Response):
    def __init__(self, response, **kwargs):
        if 'mimetype' not in kwargs and 'contenttype' not in kwargs:
            if response.startswith('<?xml'):
                kwargs['mimetype'] = 'application/xml'
        return super().__init__(response, **kwargs)

app = Flask(__name__)
app.response_class = XMLResponse

@bp.route('/feed')
def rss_feed():
    from rsshub.spiders.feed import ctx
    return render_template('main/atom.xml', ctx())
```

### 全局模板函数

有的时候你希望把某种逻辑抽象成一个函数，使得多个页面能共用，那么就要定义全局模板函数了

[有的网站](https://nyaa.si/)的排序功能是这么实现的：通过在url的查询字符串中追加sort（要排序的字段）和order（升序还是降序）参数，在视图函数中获取这2个参数并进行相应的排序处理

要实现这个功能，可以编写2个全局函数：1个负责修改url的查询字符串，另一个负责处理给查询排序

``` python
@bp.app_template_global()
def modify_querystring(**new_values):
    args = request.args.copy()
    for k, v in new_values.items():
        args[k] = v
    return f'{request.path}?{url_encode(args)}'

@bp.app_template_global()
def get_article_query():
    sort_key = request.args.get('s', 'date')
    order = request.args.get('o', 'desc')
    article_query = Article.query
    if sort_key:
        if order == 'asc':
            article_query = article_query.order_by(db.asc(sort_key))
        else:
            article_query = article_query.order_by(db.desc(sort_key))
    return article_query
```

### 自定义路由转换器

我们都知道flask的路由映射是存储在app.url\_map中的，因此查阅官方文档[有关url\_map的部分](http://flask.pocoo.org/docs/1.0/api/?highlight=url_map#flask.Flask.url_map)，就能轻松实现

``` python
from flask import Flask
from urllib.parse import unquote
from werkzeug.routing import BaseConverter

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
http://localhost:5000/list1/python+javascript+sql
http://localhost:5000/list2/python|javascript|sql
```