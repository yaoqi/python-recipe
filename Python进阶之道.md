> 如果说优雅也有缺点的话，那就是你需要艰巨的工作才能得到它，需要良好的教育才能欣赏它。
—— Edsger Wybe Dijkstra

笔者精心整理了许多实用的Python tricks，欢迎各位Pythonistia参考。

# 基本

## f-string

``` python
name = 'alphardex'
f'Ore wa {name} desu, {4 * 6} sai, gakusei desu.'
# 'Ore wa alphardex desu, 24 sai, gakusei desu.'
```

## 三元运算符

``` python
# if condition:
#    fuck
# else:
#    shit
fuck if condition else shit
```

## 字符串的拼接，反转与分割

``` python
letters = ['h', 'e', 'l', 'l', 'o']
''.join(letters)
# 'hello'
letters.reverse()
# ["o", "l", "l", "e", "h"]
name = 'nameless god'
name.split(' ')
# ['nameless', 'god']
```

## 判断元素的存在性

``` python
'fuck' in 'fuck you'
# True
'slut' in ['bitch', 'whore']
# False
'company' in {'title': 'SAO III', 'company': 'A1 Pictures'}
# True
```

# 函数

## 匿名函数

类似ES6的箭头函数，函数的简化写法，配合map、filter、sorted等高阶函数食用更佳

**注：在Python中，一般更倾向于用列表推导式来替代map和filter**

``` python
# def foo(parameters):
#     return expression
foo = lambda parameters: expression
```

### map - 映射

``` python
numbers = [1, 2, 3, 4, 5]
list(map(lambda e: e ** 2, numbers))
# [1, 4, 9, 16, 25]
```

### filter - 过滤

``` python
values = [None, 0, '', True, 'alphardex', 666]
list(filter(lambda e:e, values))
# [True, "alphardex", 666]
```

### sort - 排序

``` python
tuples = [(1, 'kirito'), (2, 'asuna'), (4, 'alice'), (3, 'eugeo')]
sorted(tuples, key=lambda x: x[1])
# [(4, 'alice'), (2, 'asuna'), (3, 'eugeo'), (1, 'kirito')]
sorted(tuples, key=lambda x: x[1], reverse=True)
# [(1, 'kirito'), (3, 'eugeo'), (2, 'asuna'), (4, 'alice')]
tuples.sort()
tuples
# [(1, 'kirito'), (2, 'asuna'), (3, 'eugeo'), (4, 'alice')]
```

其中，key这个参数接受一个函数，并将其运用在序列里的每一个元素上，所产生的结果将是排序算法依赖的对比关键字

在这个例子中，key里面的函数获取了每个元素的字符串，排序算法将会基于字母顺序进行排序

排序默认是升序，把reverse设置为True，就能按降序排序

sorted会新建一个列表作为返回值，而list.sort则是就地排序

### 其他骚操作

``` python
from functools import reduce
# 求1到100的积
reduce(lambda x, y: x * y, range(1, 101))
# 求和就更简单了
sum(range(101))
# 5050
```

扁平化列表

``` python
from functools import reduce
li = [[1,2,3],[4,5,6], [7], [8,9]]
flatten = lambda li: [item for sublist in li for item in sublist]
flatten(li)
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
# 或者直接用more_itertools这个第三方模块
# from more_itertools import flatten
# list(flatten(li))
```

## 星号和双星号

### 数据容器的合并

``` python
l1 = ['a', 'b']
l2 = [1, 2]
[*l1, *l2]
# ['a', 'b', 1, 2]
d1 = {'name': 'alphardex'}
d2 = {'age': 24}
{**d1, **d2}
# {'name': 'alphardex', 'age': 24}
```

### 函数参数的打包与解包

``` python
# 打包
def foo(*args):
    print(args)
foo(1, 2)
# (1, 2)

def bar(**kwargs):
    print(kwargs)
bar(name='alphardex', age=24)
# {'name': 'alphardex', 'age': 24}

# 解包
t = (10, 3)
quotient, remainder = divmod(*t)
quotient
# 商：3
remainder
# 余：1
```

# 数据容器

## 列表

### 推导式

笔者最爱的语法糖:)

``` python
even = [i for i in range(10) if not i % 2]
even
# [0, 2, 4, 6, 8]
```

### 同时迭代元素与其索引

用enumerate即可

``` python
li = ['a', 'b', 'c']
print([f'{i+1}. {elem}' for i, elem in enumerate(li)])
# ['1. a', '2. b', '3. c']
```

### 元素的追加与连接

append在末尾追加元素，extend在末尾连接元素

``` python
li = [1, 2, 3]
li.append([4, 5])
li
# [1, 2, 3, [4, 5]]
li.extend([4, 5])
li
# [1, 2, 3, [4, 5], 4, 5]
```

### 测试是否整体/部分满足条件

all测试所有元素是否都满足于某条件，any则是测试部分元素是否满足于某条件

``` python
all([e<20 for e in [1, 2, 3, 4, 5]])
# True
any([e%2==0 for e in [1, 3, 4, 5]])
# False
```

### 同时迭代2个以上的可迭代对象

用zip即可

``` python
subjects = ('nino', 'miku', 'itsuki')
predicates = ('saikou', 'ore no yome', 'is sky')
print([f'{s} {p}' for s, p in zip(subjects, predicates)])
# ['nino saikou', 'miku ore no yome', 'itsuki is sky']
```

### 去重

利用集合的互异性

``` python
li = [3, 1, 2, 1, 3, 4, 5, 6]
list(set(li))
# [1, 2, 3, 4, 5, 6]
# 如果要保留原先顺序的话用如下方法即可
sorted(set(li), key=li.index)
# [3, 1, 2, 4, 5, 6]
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
first, *rest = [1, 2, 3, 4]
first
# 1
rest
# [2, 3, 4]
```

## 字典

### 遍历

``` python
d = {'name': "alphardex", 'age': 24}
[key for key in d.keys()]
# ['name', 'age']
[value for value in d.values()]
# ['alphardex', 24]
[f'{key}: {value}' for key, value in d.items()]
# ['name: alphardex', 'age: 24']
```

### 排序

``` python
import operator
data = [{'rank': 2, 'author': 'alphardex'}, {'rank': 1, 'author': 'alphardesu'}]
data_by_rank = sorted(data, key=operator.itemgetter('rank'))
data_by_rank
# [{'rank': 1, 'author': 'alphardesu'}, {'rank': 2, 'author': 'alphardex'}]
data_by_rank_desc = sorted(data, key=lambda x: x['rank'], reverse=True)
# [{'rank': 2, 'author': 'alphardex'}, {'rank': 1, 'author': 'alphardesu'}]
```

### 反转

``` python
d = {'name': 'alphardex', 'age': 24}
{v: k for k, v in d.items()}
# {'alphardex': 'name', 24: 'age'}
```

# 语言专属特性

## 下划线\_的几层含义

### repl中暂存结果

``` python
>>> 1 + 1
# 2
>>> _
# 2
```

### 忽略某个变量

``` python
filename, _ = 'eroge.exe'.split('.')
filename
# 'eroge'
for _ in range(2):
    print('wakarimasu')
# wakarimasu
# wakarimasu
```

### i18n国际化

``` python
_("This sentence is going to be translated to other language.")
```

### 增强数字的可读性

``` python
1_000_000
# 1000000
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

with open_write('onegai.txt') as f:
    f.write('Dagakotowaru!')
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

## 多重继承

在django中经常要处理类的多重继承的问题，这时就要用到super函数

如果单单认为super仅仅是“调用父类的方法”，那就错了

**其实super指的是MRO中的下一个类**，用来解决多重继承时父类的查找问题

MRO是啥？Method Resolution Order（方法解析顺序）

看完下面的例子，就会理解了

``` python
class A:
    def __init__(self):
        print('A')

class B(A):
    def __init__(self):
        print('enter B')
        super().__init__()
        print('leave B')

class C(A):
    def __init__(self):
        print('enter C')
        super().__init__()
        print('leave C')

class D(B, C):
    pass

d = D()
# enter B
# enter C
# A
# leave C
# leave B
print(d.__class__.__mro__)
# (<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)
```

首先，因为D继承了B类，所以调用B类的\_\_init\_\_，打印了`enter B`

打印`enter B`后的super寻找MRO中的B的下一个类，也就是C类，并调用其\_\_init\_\_，打印`enter C`

打印`enter C`后的super寻找MRO中的C的下一个类，也就是A类，并调用其\_\_init\_\_，打印`A`

打印`A`后回到C的\_\_init\_\_，打印`leave C`

打印`leave C`后回到B的\_\_init\_\_，打印`leave B`

## 特殊方法

在django中，定义model的时候，希望admin能显示model的某个字段而不是XXX Object，那么就要定义好\_\_str\_\_

每当你使用一些内置函数时，都是在调用一些特殊方法，例如len()调用了\_\_len\_\_(), str()调用\_\_str\_\_()等

以下实现一个数学向量类，里面有6个特殊方法

``` python
from math import hypot

class Vector:
    # 实例创建
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    # 字符串表示形式
    def __repr__(self) -> str:
        return f'Vector({self.x}, {self.y})'

    # 数值转换 - 绝对值
    def __abs__(self) -> float:
        return hypot(self.x, self.y)

    # 数值转换 - 布尔值
    def __bool__(self) -> bool:
        return bool(abs(self))

    # 算术运算符 - 加
    def __add__(self, other: Vector) -> Vector:
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)

    # 算术运算符 - 乘
    def __mul__(self, scalar: float) -> Vector:
        return Vector(self.x * scalar, self.y * scalar)

v = Vector(3, 4)

# repr(v) => v.__repr__()
v
# Vector(3, 4)

# abs(v) => v.__abs__()
abs(v)
# 5.0

# bool(v) => v.__bool__()
bool(v)
# True

# v1 + v2  => v1.__add__(v2)
v1 = Vector(1, 2)
v2 = Vector(3, 4)
v1 + v2
# Vector(4, 6)

# v * 3  => v.__mul__(3)
v * 3
# Vector(9, 12)
```

想了解所有的特殊方法可查阅[官方文档](https://docs.python.org/3/reference/datamodel.html#special-method-names),以下列举些常用的：

``` python
字符串表示形式：__str__, __repr__
数值转换：__abs__, __bool__, __int__, __float__, __hash__
集合模拟：__len__, __getitem__, __setitem__, __delitem__, __contains__
迭代枚举：__iter__, __reversed__, __next__
可调用模拟：__call__
实例创建与销毁：__init__, __del__
运算符相关：__add__, __iadd__, __mul__, __imul__
```
