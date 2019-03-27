如今，网上的爬虫教程可谓是泛滥成灾了，从urllib开始讲，最后才讲到requests和selenium这类高级库，实际上，根本就不必这么费心地去了解这么多无谓的东西的。只需记住爬虫总共就三大步骤：发起请求——解析数据——存储数据，这样就足以写出最基本的爬虫了。诸如像Scrapy这样的框架，可以说是集成了爬虫的一切，但是新人可能会用的不怎么顺手，看教程可能还会踩各种各样的坑，而且Scrapy本身体积也有点大。因此，本人决定亲手写一个轻量级的爬虫框架————[looter](https://github.com/alphardex/looter)，里面集成了调试和爬虫模板这两个核心功能，利用looter，你就能迅速地写出一个高效的爬虫。另外，本项目的函数文档也相当完整，如果有不明白的地方可以自行阅读源码（一般都是按Ctrl+左键或者F12）。

<!--more-->

## 安装

``` bash
$ pip install looter
```

仅支持Python3.6及以上版本。

## 快速开始

让我们先来撸一个非常简单的图片爬虫：首先，用shell获取网站

``` bash
$ looter shell konachan.com/post
```

然后用1行代码将图片的url提取出来

``` python
>>> imgs = tree.css('a.directlink::attr(href)').extract()
```

或者用另一种方式提取

``` python
>>> imgs = links(res, pattern=r'.*/(jpeg|image)/.*')
```

将url保存到本地

``` python
>>> Path('konachan.txt').write_text('\n'.join(imgs))
```

可以通过wget等下载工具将图片下载下来

``` bash
$ wget -i konachan.txt
```

如果想要看更多的爬虫例子，[猛戳这里](https://github.com/alphardex/looter/tree/master/looter/examples)

## 工作流

如果你想迅速撸出一个爬虫，那么你可以用looter提供的模板来自动生成一个

``` bash
$ looter genspider <name> [--async]
```

async是一个备用的选项，它使得生成的爬虫核心用asyncio而非线程池。

在生成的模板中，你可以自定义domain和tasklist这两个变量。

什么是tasklist？实际上它就是你想要抓取的页面的所有链接。

以konachan.com为例，你可以使用列表推导式来创建自己的tasklist：

``` python
domain = 'https://konachan.com'
tasklist = [f'{domain}/post?page={i}' for i in range(1, 9777)]
```

然后你就要定制你的crawl函数，这是爬虫的核心部分。

``` python
def crawl(url):
    tree = lt.fetch(url)
    items = tree.css('ul li')
    for item in items:
        data = {}
        # data[...] = item.css(...)
        pprint(data)
```

在大多数情况下，你所要抓取的内容是一个列表（也就是HTML中的ul或ol标签），可以用css选择器将它们保存为items变量。

然后，你只需使用for循环来迭代它们，并抽取你想要的数据，将它们存储到dict中。

**注意：目前looter使用了parsel来解析网页，和Scrapy的解析工具一样。如果想用以前的cssselect的话，把fetch的use\_parsel设为False就可以了。**

但是，在你写完这个爬虫之前，最好用looter提供的shell来调试一下你的css代码是否正确。（目前已集成[ptpython](https://github.com/jonathanslenders/ptpython)，一个支持自动补全的REPL）

``` python
>>> items = tree.css('ul li')
>>> item = items[0]
>>> item.css(anything you want to crawl)
# 注意代码的输出是否正确！
```

调试完成后，你的爬虫自然也就完成了。怎么样，是不是很简单:)

## 函数

looter为用户提供了一些比较实用的函数。

### view

在爬取页面前，你最好确认一下页面的渲染是否是你想要的

``` python
>>> view(url)
```

### links

获取网页的所有链接

``` python
>>> links(res)                  # 获取所有链接
>>> links(res, search='...')    # 查找指定链接
>>> links(res, pattern=r'...')  # 正则查找链接
```

### save\_as\_json

将所得结果保存为json文件，支持按键值排序（sort\_by)和去重(no\_duplicate)

``` python
>>> total = [...]
>>> save_as_json(total, sort_by='key', no_duplicate=True)
```

如果想保存为别的格式（csv、xls等），用pandas转化即可

``` python
>>> import pandas as pd
>>> data = pd.read_json('xxx.json')
>>> data.to_csv()
```

### login

有一些网站必须要先登录才能爬取，于是就有了login函数，本质其实就是建立session会话向服务器发送带有data的POST请求。
考验各位抓包的能力，以下为模拟登录网易126邮箱（要求参数：postdata和param）

``` python
>>> params = {'df': 'mail126_letter', 'from': 'web', 'funcid': 'loginone', 'iframe': '1', 'language': '-1', 'passtype': '1', 'product': 'mail126',
 'verifycookie': '-1', 'net': 'failed', 'style': '-1', 'race': '-2_-2_-2_db', 'uid': 'webscraping123@126.com', 'hid': '10010102'}
>>> postdata = {'username': 你的用户名, 'savelogin': '1', 'url2': 'http://mail.126.com/errorpage/error126.htm', 'password': 你的密码}
>>> url = "https://mail.126.com/entry/cgi/ntesdoor?"
>>> res, ses = login(url, postdata, params=params) # res为post请求后的页面，ses为请求会话
>>> index_url = re.findall(r'href = "(.*?)"', res.text)[0] # 在res中获取重定向主页的链接
>>> index = ses.get(index_url) # 用ses会话访问重定向链接，想确认成功的话print下即可
```

## 套路总结

1. 通过抓包，确认网站是否开放了api，如果有，直接抓取api；如果没有，进入下一步
2. 确认网站是静态的还是动态的（有无JS加载，是否需要登录等），方法有：肉眼观察、抓包、looter的view函数
3. 若网站是静态网页，直接用looter genspider生成爬虫模板，再配合looter shell写出爬虫即可
4. 若网站是动态网页，先抓包试试，尝试获取所有ajax生成的api链接；如果没有api，则进入下一步
5. 有的网站并不会直接暴露ajax的api链接，这时就需要你自行根据规律，构造出api链接
6. 如果上一步无法成功，那么就只好用[requestium](https://github.com/tryolabs/requestium)来渲染JS，抓取页面
7. 至于[模拟登录](https://github.com/xchaoinfo/fuck-login)、[代理IP](https://github.com/imWildCat/scylla)、验证码、分布式等问题，由于范围太广，请自行解决
8. 如果你的爬虫项目被要求用Scrapy，那么你也可以将looter的解析代码无痛地复制到Scrapy上（毕竟都用了parsel）

掌握了以上的套路，再难爬的网站也难不倒你。