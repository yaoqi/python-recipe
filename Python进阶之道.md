> 如果说优雅也有缺点的话，那就是你需要艰巨的工作才能得到它，需要良好的教育才能欣赏它。
—— Edsger Wybe Dijkstra

笔者精心整理了许多实用的Python tricks，欢迎各位Pythonistia参考。
阅读本文前有三点要提醒大家：

1. 请确保你的Python是3.6以上的版本
2. 本文仅仅谈Python语言本身，标准库请看[另一篇文章](https://zhuanlan.zhihu.com/p/37240446)
3. 由于篇幅限制，略过了有的需要细讲的地方（生成器和装饰器），如果想进一步了解请参考官方文档或相关书籍

每个优秀的Pythonista都应当遵循The Zen of Python

``` python
>>> import this
```

<!--more-->

# 基本

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

## 字符串的拼接，反转与分割

``` python
>>> letters = ['h', 'e', 'l', 'l', 'o']
>>> ''.join(letters)
'hello'
>>> letters.reverse()
["o", "l", "l", "e", "h"]
>>> name = 'nameless god'
>>> name.split(' ')
['nameless', 'god']
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

# 函数

## lambda

类似ES6的箭头函数，函数的简化写法，配合map、filter、sort等食用更佳

``` python
# def foo(parameters):
#     return expression
foo = lambda parameters: expression
```

### map - 映射

``` python
>>> numbers = [1, 2, 3, 4, 5]
>>> list(map(lambda e: e ** 2, numbers))
[1, 4, 9, 16, 25]
```

### filter - 过滤

``` python
values = [None, 0, '', True, 'alphardex', 666]
list(filter(lambda e:e, values))
[True, "alphardex", 666]
```

### sort - 排序

``` python
>>> tuples = [(1, 'kirito'), (2, 'asuna'), (4, 'alice'), (3, 'eugeo')]
>>> sorted(tuples, key=lambda x: x[1])
[(4, 'alice'), (2, 'asuna'), (3, 'eugeo'), (1, 'kirito')]
```

### 其他骚操作

求1到100的积

``` python
>>> from functools import reduce
>>> reduce(lambda x, y: x * y, range(1, 101))
# 求和就更简单了
>>> sum(range(101))
```

扁平化列表

``` python
>>> from functools import reduce
>>> li = [[1,2,3],[4,5,6], [7], [8,9]]
>>> flatten = lambda li: [item for sublist in li for item in sublist]
>>> flatten(li)
# 或者直接用more_itertools这个第三方模块
# from more_itertools import flatten
# list(flatten(li))
[1, 2, 3, 4, 5, 6, 7, 8, 9]
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

# 数据容器

## 列表

### 推导式

笔者最爱的语法糖:)

``` python
>>> even = [i for i in range(10) if not i % 2]
>>> even
[0, 2, 4, 6, 8]
```

### 同时迭代元素与其索引

用enumerate即可

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

### 同时迭代2个以上的可迭代对象

用zip即可

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
# 如果要保留原先顺序的话用如下方法即可
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

利用它反转字典简直小菜一碟

``` python
>>> d = {'name': 'alphardex', 'age': 24}
>>> {v: k for k, v in d.items()}
{'alphardex': 'name', 24: 'age'}
```

### 遍历键值对

``` python
>>> d = {'name': "alphardex", 'age': 24}
>>> [f'{key}: {value}' for key, value in d.items()]
['name: alphardex', 'age: 24']
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

# 语言专属特性

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

## 静态类型注解

给函数参数添加类型，能提高代码的可读性和可靠性，大型项目的最佳实践之一

``` python
from typing import List

def greeting(name: str) -> str:
    return f'Hello {name}.'

def gathering(users: List[str]) -> str:
    return f"{', '.join(users)} are going to be raped."

print(greeting('alphardex'))
print(gathering(['Bitch', 'slut']))
```

# 编程技巧

- 善用包管理器管理软件包以及各种库（例如windows系统下的chocolatey、python语言的pip、node.js语言的npm等）
- 善用IDE或vscode编辑器的代码跳转功能（F12键或者Ctrl+鼠标左键），在使用一个复杂的函数前必须看其源码
- 善用自省（尤其是dir和help函数），能使你不用去死记函数的功能（因为函数一般都会有完善的文档）
- 给每一个函数写好docstring
- 遵循PEP8规范，用好pylint和yapf（分别用于代码的检查和格式化）
- 善于查阅在线API文档（例如[devdocs.io](https://devdocs.io/)）
- 搞个能自动补全代码的神器：[ptpython](https://github.com/jonathanslenders/ptpython)
- 善用google和stackoverflow来解决问题
- 为了理解Python代码的执行过程，可以用[pythontutor](http://www.pythontutor.com/visualize.html)
- 英语一定要有底子，起码不能成为读文档的障碍
