> 如果说优雅也有缺点的话，那就是你需要艰巨的工作才能得到它，需要良好的教育才能欣赏它。
—— Edsger Wybe Dijkstra

笔者精心整理了许多实用的Python tricks，欢迎各位Pythonistia参考。
阅读本文前有三点要提醒大家：

1. 请确保你的Python是3.6以上的版本
2. 本文仅仅谈Python语言本身，标准库请看[另一篇文章](https://zhuanlan.zhihu.com/p/37240446)
3. 由于篇幅限制，有的地方可能显得过于简略（比如生成器、装饰器和OOP），如果想进一步了解请参考官方文档或相关书籍

<!--more-->

# 基本

## Python之禅

每个优秀的Pythonista都应当遵循

``` python
>>> import this
```

## f-string

速度最快的格式化字符串

``` python
>>> name = 'alphardex'
>>> f'Ore wa {name} desu, {4 * 6} sai, gakusei desu.'
'Ore wa alphardex desu, 24 sai, gakusei desu.'
```

## 三元运算符

将if语句压扁成1行

``` python
# if condition:
#    fuck
# else:
#    shit
fuck if condition else shit
```

## 字符串的拼接与分割

``` python
>>> letters = ['h', 'e', 'l', 'l', 'o']
>>> ''.join(letters)
'hello'
>>> name = 'nameless god'
>>> name.split(' ')
['nameless', 'god']
```

## 链式比较

``` python
>>> 1 < 2 < 3
True
```

## 判断元素的存在性

用in操作符可以判断一个元素是否存在于另一个元素中

``` python
>>> 'fuck' in 'fuck you'
True
>>> 'slut' in ['bitch', 'whore']
False
>>> 'company' in {'title': 'SAO III', 'company': 'A1 Pictures'}
True
```

## 下划线\_的几层含义

### repl中暂存结果

``` python
>>> 1 + 1
2

>>> _
2
```

### 忽略某个变量

``` python
>>> filename, _ = 'eroge.exe'.split('.')
>>> filename
'eroge'
>>> for _ in range(2):
    print('wakarimasu')
```

### i18n国际化

``` python
_("This sentence is going to be translated to other language.")
```

### 增强数字的可读性

``` python
>>> 1_000_000
1000000
```

## 上下文管理器

用于资源的获取与释放，以代替try-except语句

常用于文件IO，锁的获取与释放，数据库的连接与断开等

``` python
# try:
#     f = open(input_path)
#     data = f.read()
# finally:
#     f.close()
with open(input_path) as f:
    data = f.read()
```

可以用@contextmanager来实现上下文管理器

``` python
from contextlib import contextmanager

@contextmanager
def open_write(filename):
    try:
        f = open(filename, 'w')
        yield f
    finally:
        f.close()

>>> with open_write('onegai.txt') as f:
... f.write('Dagakotowaru!')
```

# 函数

## 静态类型注解

给参数添加类型，能提高代码的可读性和可靠性，大型项目的最佳实践之一

``` python
from typing import List

def greeting(name: str) -> str:
    return f'Hello {name}.'

def gathering(users: List[str]) -> str:
    return f"{', '.join(users)} are going to be raped."

print(greeting('alphardex'))
print(gathering(['Bitch', 'slut']))
```

## 返回多个值

返回一个tuple即可

``` python
def load_dataset():
    # after processing the data
    return ds_train, ds_test

ds_train, ds_test = load_dataset()
```

## lambda

类似ES6的箭头函数，函数的简化写法，配合map、filter、sort等食用更佳

``` python
# def foo(parameters):
#     return expression
foo = lambda parameters: expression
```

比如作为排序函数sorted的key

``` python
>>> tuples = [(1, 'kirito'), (2, 'asuna'), (4, 'alice'), (3, 'eugeo')]
>>> sorted(tuples, key=lambda x: x[1])
[(4, 'alice'), (2, 'asuna'), (3, 'eugeo'), (1, 'kirito')]
```

亦或是作为map函数的function

例子：获取当前路径下所有pdf文件的路径并转为字符串

``` python
>>> from pathlib import Path
>>> pdf_paths = list(map(str, Path('.').glob('*.pdf')))
```

实现1到100的积

``` python
>>> from functools import reduce
>>> reduce(lambda x, y: x * y, range(1, 101))
# 求和就更简单了
>>> sum(range(101))
```

## 偏函数

用于固定原函数的某些参数，在此基础上造出一个新的函数

``` python
>>> from functools import partial
>>> multiply = lambda a, b: a * b
>>> double = partial(multiply, 2)
>>> multiply(3, 4)
12
>>> double(4)
8
```

## 文档字符串

一个好的函数必定有文档字符串，用来解释说明这个函数的作用。

vscode有一个插件叫autoDocstring，能够根据参数类型自动生成文档字符串

就拿我写的looter框架里的一个函数为例子吧，文档字符串是google风格的

``` python
def save_as_json(total: list, name='data.json', sort_by: str=None, no_duplicate=False, order='asc'):
    """Save what you crawled as a json file.
    Args:
        total (list): Total of data you crawled.
        name (str, optional): Defaults to 'data'. The name of the file.
        sort_by (str, optional): Defaults to None. Sort items by a specific key.
        no_duplicate (bool, optional): Defaults to False. If True, it will remove duplicated data.
        order (str, optional): Defaults to 'asc'. The opposite option is 'desc'.
    """
    if sort_by:
        reverse = True if order == 'desc' else False
        total = sorted(total, key=itemgetter(sort_by), reverse=reverse)
    if no_duplicate:
        unique = []
        for obj in total:
            if obj not in unique:
                unique.append(obj)
        total = unique
    data = json.dumps(total, ensure_ascii=False)
    Path(name).write_text(data, encoding='utf-8')
```

## 星号和双星号

### 数据容器的合并

``` python
>>> l1 = ['a', 'b']
>>> l2 = [1, 2]
>>> [*l1, *l2]
['a', 'b', 1, 2]
>>> d1 = {'name': 'alphardex'}
>>> d2 = {'age': 24}
>>> {**d1, **d2}
{'name': 'alphardex', 'age': 24}
```

### 函数参数的打包

在函数中可以说是位置参数和关键字参数的打包

``` python
>>> def foo(*args):
...     print(args)
>>> foo(1, 2)
(1, 2)
>>> def bar(**kwargs):
...     print(kwargs)
>>> bar(name='alphardex', age=24)
{'name': 'alphardex', 'age': 24}
```

## 生成器

只能迭代一次的迭代器，用于实现惰性求值

``` python
def get123():
    yield 1
    yield 2
    yield 3

>>> print(sum(get123()))
6
>>> generator = get123()
>>> sum(generator)
6
>>> sum(generator)
0
>>> even = (i for i in range(10) if not i % 2)
>>> list(even)
[0, 2, 4, 6, 8]
```

## 装饰器

不修改原函数的基础上给函数添加额外功能

常用于路由映射（典型例子：flask），记录日志性能，数据库事务等

只要记住一点就行：**函数也是对象。**

``` python
import time
from functools import wraps

def log_time(func):
    @wraps(func) # 保留函数的元信息
    def wrapper(*args, **kwargs):
        start = time.time()
        func(*args, **kwargs)
        end = time.time()
        print(f'{func.__name__} cost time: {end - start}')
    return wrapper

@log_time
def hello():
    time.sleep(1)
    print('Hello decorator!')

>>> hello()
Hello decorator!
hello cost time: 1.0009078979492188
```

# 数据容器

## 列表

### 切片

第三个参数是步长，可以为负，例如反转字符串

``` python
>>> 'hello'[::-1]
'olleh'
```

### 推导式

笔者最爱的语法糖:)

``` python
>>> even = [i for i in range(10) if not i % 2]
>>> even
[0, 2, 4, 6, 8]
```

### enumerate

用于同时迭代元素与其索引

``` python
>>> li = ['a', 'b', 'c']
>>> print([f'{i+1}. {elem}' for i, elem in enumerate(li)])
['1. a', '2. b', '3. c']
```

### append和extend

前者在末尾追加元素，后者在末尾连接元素

``` python
>>> li = [1, 2, 3]
>>> li.append([4, 5])
>>> li
[1, 2, 3, [4, 5]]
>>> li.extend([4, 5])
>>> li
[1, 2, 3, [4, 5], 4, 5]
```

### zip

同时迭代2个以上的可迭代对象

``` python
subjects = ('nino', 'miku', 'itsuki')
predicates = ('saikou', 'ore no yome', 'is sky')
print([f'{s} {p}' for s, p in zip(subjects, predicates)])
```

### 去重

利用集合的互异性

``` python
>>> li = [3, 1, 2, 1, 3, 4, 5, 6]
>>> list(set(li))
[1, 2, 3, 4, 5, 6]
```

如果想保留原先的顺序呢？用sorted就行了

``` python
>>> li = [3, 1, 2, 1, 3, 4, 5, 6]
>>> sorted(set(li), key=li.index)
[3, 1, 2, 4, 5, 6]
```

### 解包

此法亦适用于元组等可迭代对象

最典型的例子就是2数交换

``` python
a, b = b, a
# 等价于 a, b = (b, a)
```

用星号运算符解包可以获取剩余的元素

``` python
>>> first, *rest = [1, 2, 3, 4]
>>> first
1
>>> rest
[2, 3, 4]
```

## 字典

### 推导式

``` python
{key: value for key, value in iterable}
```

反转字典简直小菜一碟

``` python
>>> d = {'name': 'alphardex', 'age': 24}
>>> {v: k for k, v in d.items()}
{'alphardex': 'name', 24: 'age'}
```

### get

获取键值，可以处理缺失值

``` python
>>> d = {'name': 'alphardex'}
>>> d.get('name')
'alphardex'
>>> d.get('age')
>>> d.get('age', 24)
24
```

### switch实现

利用表驱动

``` python
# if current_engine = 'baidu':
#     current_url_pattern = 'https://www.baidu.com/s?wd={w}&sa=tb'
# elif current_engine = 'sougou':
#     current_url_pattern = 'https://www.sogou.com/web?query={w}&s_from=index'
engine_url_patterns = {
    'baidu': 'https://www.baidu.com/s?wd={w}&sa=tb',
    'sougou': 'https://www.sogou.com/web?query={w}&s_from=index'
}
current_url_pattern = engine_url_patterns.get(current_engine)
```

### 键值对排序

``` python
import operator
data = [{'rank': 2, 'author': 'alphardex'}, {'rank': 1, 'author': 'alphardesu'}]
data_by_rank = sorted(data, key=operator.itemgetter('rank'))
# 其实了解lambda的话也可以这么写
data_by_rank = sorted(data, key=lambda x: x['rank'])
# sorted的排序默认是asc（升序），可以用reverse选项来实现desc（降序）
data_by_rank_desc = sorted(data, key=lambda x: x['rank'], reverse=True)
```

# OOP

- super: 实现子类调用父类的方法, super(SuperClass).\_\_init\_\_()
- 元类: 生产类的工厂，典型的例子是ORM的实现
- @classmethod和@staticmethod: 前者绑定cls本身，后者啥都不绑定
- @property: 把类的方法变成属性调用
- 魔术方法：所有以“\_\_”双下划线包起来的方法，请查阅官方文档，很重要
- 鸭子类型：关注的不是对象的类型本身，而是它是如何使用的
- 猴子补丁: 在运行时替换类的方法和属性

# 编程技巧

- 善用包管理器管理软件包以及各种库（例如windows系统下的chocolatey、python语言的pip、node.js语言的npm等）
- 善用IDE或vscode编辑器的代码跳转功能（F12键或者Ctrl+鼠标左键），在使用一个复杂的函数前必须看其源码
- 善用自省（尤其是dir和help函数），能使你不用去死记函数的功能（因为函数一般都会有完善的文档）
- 遵循PEP8规范，用好pylint和yapf（分别用于代码的检查和格式化）
- 善于查阅在线API文档（例如[devdocs.io](https://devdocs.io/)）
- 搞个能自动补全代码的神器：[ptpython](https://github.com/jonathanslenders/ptpython)
- 善用google和stackoverflow来解决问题
- 为了理解Python代码的执行过程，可以用[pythontutor](http://www.pythontutor.com/visualize.html)
- 英语一定要有底子，起码不能成为读文档的障碍
