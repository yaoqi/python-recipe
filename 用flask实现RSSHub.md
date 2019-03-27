RSSHub是一个RSS生成器，其实它的本质就是爬虫的集合。

由于爬虫是Python的强项，因此笔者就尝试用Python的flask框架来实现它。

项目地址：https://github.com/alphardex/RSSHub-python

DEMO地址：https://rsshub-python.herokuapp.com/

<!--more-->

## RSS入门

**注：以下把RSS信息统称为信息源。**

RSS（简易信息聚合）是目前常见的一种信息订阅方式。用户只需有一个RSS阅读器，就能订阅各种各样的信息源了。

有的人会推荐inoreader，但笔者觉得它在国内速度太慢，其次有的功能还要付费，不怎么推荐。

笔者要推荐的是这款[irreader](https://github.com/fateleak/irreader)，不仅速度快，而且信息源也很丰富，用户体验更佳。

## RSSHub

当你有了RSS阅读器以后，就得寻找自己的信息源了。

笔者逛了逛Github，发现[RSSHub](https://github.com/diygod/rsshub)真是个好东西，里面有各种社区贡献的信息源，但笔者一看全是JS写的爬虫脚本。

由于笔者JS的功力没Python深厚，因此就开始考虑用Python来实现一个RSSHub，这样一来爬虫脚本就也能用Python写了XD。

日后笔者会把想订阅的东西都放到这里

## RSS获取途径

想获取RSS源其实很容易，只要按照以下顺序就行：

1. 网站自带的RSS（通常是“订阅”）
2. 在[RSSHub](https://docs.rsshub.app)上按Ctrl+F搜索（用[笔者的版本](https://rsshub-python.herokuapp.com/feeds)也行）
3. 在RSS阅读器（例如inoreader）上搜索RSS源
4. 利用搜索引擎搜索RSS源
5. 尝试在想订阅的网站url后面追加rss和atom.xml
6. 自己用Python写一个RSS源并给[RSSHub-python](https://github.com/alphardex/RSSHub-python)提PR

## 技术RSS源

用以上的办法，笔者挑选出了一些技术RSS源：

- [博客园精华区](http://feed.cnblogs.com/blog/picked/rss)
- [CTOLib码库Python专区](https://rsshub-python.herokuapp.com/ctolib/topics/last)
- [infoq推荐内容](https://rsshub-python.herokuapp.com/infoq/recommend)
- [segmentfault](https://segmentfault.com/feeds/blogs)
- [开发者头条](https://rsshub.app/toutiao/today)
- [掘金后端](https://rsshub.app/juejin/category/backend)
- [开源中国资讯](https://rsshub.app/oschina/news)
