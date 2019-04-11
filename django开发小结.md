最近把[《django by example》](https://book.douban.com/subject/26679450/)的项目差不多都撸完了，是时候该写个小结了。

个人体会：django的api可以说是很多很全，这辈子都不可能全记住的。

其实，懂得速查[文档](https://devdocs.io/django/)就没有什么大问题。

大量的封装虽然牺牲了一定的灵活性，但大大提高了开发效率，或许这就是django的哲学吧。

## 准备

首先，你可以用django-admin来生成你的项目。

不过笔者更建议用一个[脚手架](https://github.com/alphardex/cookiecutter-django-bootstrap)来快速生成一个启动模板。

## 核心工作流

Django的核心是MVC，更准确来说是MVT（Model-View-Template）

首先创建app，并在settings中的INSTALLED\_APPS添加其配置，在全局urls中通过include引入app的所有url

接下来才是最关键的3步：

1. 在models.py中定义好数据模型并迁移它们
2. 在views.py中编写视图函数，并在urls.py中为其创建相应的映射
3. 在templates文件夹中编写要渲染的模板HTML

数据模型的定义其实就是定义各种各样的字段，还有个Meta类可以定义一些元数据（比如字段的排序等），此外你也可以为模型封装一些method来简化视图的编写。

视图函数的编写主要涉及以下方面：模型的CRUD、表单的处理、模板的上下文渲染（必须熟悉HTTP）

视图函数分2种：函数和类（即FBV和CBV）。两者各有利弊，根据需求自行权衡。目前来说后者在django中比较流行。

给视图添加额外功能：FBV用装饰器，CBV用Mixin。

模板的编写主要涉及：上下文的渲染、if、for、with语句、过滤器、继承等

## 后台管理

其实在定义完数据模型后就已经可以从admin开始玩起了（初次进入需要创建一个超级用户）。

把数据模型通通注册上去，以便进行CRUD。

## 其他杂项

- 数据模型的Meta元信息（ordering、abstract等）
- 利用Manager来简化查询
- 熟悉QuerySet的各种接口和查询方法（比如Q、select\_related等）
- 懂得如何优化查询
- 利用Library实现自定义标签
- 创建sitemaps来优化SEO
- 创建feeds来提供RSS订阅功能
- 利用Postgresql来实现全文搜索
- 利用Ajax来优化用户体验
- 利用contenttypes来追踪models（比如实现用户活动流）
- 利用signals来反规范化计数
- 利用Redis实现各种功能（缓存、计数、排行榜等）
- 利用sessions在服务端存储数据（比如购物车）
- 利用celery实现异步任务（比如邮件发送）
- 定制admin
- 数据模型的继承（abstract、multi-table、proxy）
- 自定义数据模型字段
- 用Mixin为类视图添加额外功能
- 用formset处理多张表单
- 缓存的使用
- 用drf创建RESTful API
- 自定义中间件
- 部署上线

如果对以上内容都了然于胸的话，可以说是掌握django了。

当然，以上的所有内容用flask也都可以实现，只是方式不同罢了:)

## 常用第三方库

- django-debug-toolbar：提供DEBUG信息，必备
- django-crispy-forms：美化表单
- django-extensions：各种扩展（shell、server等）
- django-taggit：提供简单的打标签功能
- django-braces：为类视图提供一系列Mixin
- django-embed-video：为页面嵌入视频
- djangorestframework：大名鼎鼎的drf，为django提供RESTful API支持
- django-xadmin：一个更强大的admin后台
- django-ckeditor：为表单提供富文本编辑器
- markdown：提供Markdown渲染支持
- pillow：图像处理
- sorl-thumbnail：缩略图生成
- redis：redis数据库的接口
- celery：分布式任务队列，用来任务调度
- flower：监控celery
- weasyprint：用HTML生成PDF文件
