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

类似ES6的箭头函数，函数的简化写法，配合map、filter、sort等食用更佳

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
```

### 其他骚操作

求1到100的积

``` python
from functools import reduce
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

## 偏函数

用于固定原函数的某些参数，在此基础上造出一个新的函数

``` python
from functools import partial
multiply = lambda a, b: a * b
double = partial(multiply, 2)
multiply(3, 4)
# 12
double(4)
# 8
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

### 函数参数的打包

``` python
def foo(*args):
    print(args)
foo(1, 2)
# (1, 2)

def bar(**kwargs):
    print(kwargs)
bar(name='alphardex', age=24)
# {'name': 'alphardex', 'age': 24}
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
# 其实了解lambda的话也可以这么写：data_by_rank = sorted(data, key=lambda x: x['rank'])
# sorted的排序默认是asc（升序），可以用reverse选项来实现desc（降序）
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
