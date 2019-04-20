参考repo：https://github.com/hemanth/functional-programming-jargon

## Arity - 函数参数个数

``` python
import inspect
add = lambda a, b: a + b
len(inspect.getfullargspec(add).args)
# 2
```

## Higher-Order Function - 高阶函数

以函数为参数或返回值

``` python
is_type = lambda type_: lambda x: isinstance(x, type_)
li = [0, '1', 2, None]
[l for l in li if is_type(int)(l)]
# [0, 2]
```

## Closure - 闭包

闭包是一种在变量作用域之外访问变量的方法。是一种将函数存储在环境中的方法。

闭包是一个作用域，它捕获函数的局部变量以便访问，即使在执行已经移出定义它的块之后也是如此。

``` python
add_to = lambda x: lambda y: x + y
add_to_five = add_to(5)
add_to_five(3)
# 8
```

函数addTo()返回一个函数（内部称为add()），将它存储在名为addToFive的变量中，它带有参数为5的柯里化调用。

理想情况下，当函数addTo完成执行时，其作用域与局部变量add，x，y就变得不可访问。 但是，它在调用addToFive()时返回8。 这意味着即使在代码块完成执行后也会保存函数addTo的状态，否则无法知道addTo被调用为addTo(5)并且x的值被设置为5。

词法作用域范围是它能够找到x和add的值的原因 - 已经完成执行的父项的私有变量。该值称为闭包。

堆栈以及函数的词法范围以对父项的引用形式存储。这可以防止关闭和底层变量被垃圾收集（因为至少有一个对它的实时引用）。

闭包是一种通过引用其主体外部的字段来包围其周围状态的函数。封闭状态保持在闭包的调用之间。

## Partial Function - 偏函数

通过对原始函数预设参数来创建一个新的函数

``` python
from functools import partial
add3 = lambda a, b, c: a + b + c
five_plus = partial(add3, 2, 3)
five_plus(4)
# 9
```

## Currying - 柯里化

将一个多元函数转变为一元函数的过程

``` python
add = lambda a, b: a + b
curried_add = lambda a: lambda b: a + b
curried_add(3)(4)
# 7
add2 = curried_add(2)
add2(10)
# 12
```

## Auto Currying - 自动柯里化

``` python
from toolz import curry
add = lambda a, b: a + b
curried_add = curry(add)
curried_add(1, 2)
# 3
curried_add(1)(2)
# 3
curried_add(1)
# <function <lambda> at 0x000002088BBD5E18>
```

## Function Composition - 函数组合

接收多个函数作为参数，从右到左，一个函数的输入为另一个函数的输出

``` python
import math
from functools import reduce
# 组合2个函数
compose = lambda f, g: lambda a: f(g(a))
# 组合多个函数
compose = lambda *funcs: reduce(lambda f, g: lambda *args: f(g(*args)), funcs)
floor_and_to_string = compose(str, math.floor)
floor_and_to_string(12.12)
# '12'
```

## Purity - 纯函数

输出仅由输入决定，且不产生副作用

``` python
greet = lambda name: f'hello, {name}'
greet('world')
'hello, world'
```

以下代码不是纯函数

``` python
# 情况1：函数依赖全局变量
NAME = 'alphardex'
greet = lambda: f'hi, {NAME}'
greet()
# 'hi, alphardex'

# 情况2：函数修改了全局变量
greeting = None
def greet(name):
    global greeting
    greeting = f'hi, {name}'
greet('alphardex')
greeting
# 'hi, alphardex'
```

## Side effects - 副作用

如果函数与外部可变状态进行交互，则它是有副作用的

最典型的例子是创建日期和IO

``` python
from datetime import datetime
different_every_time = datetime.now()
different_every_time
# datetime.datetime(2019, 4, 20, 17, 30, 24, 824876)
different_every_time = datetime.now()
different_every_time
# datetime.datetime(2019, 4, 20, 17, 31, 41, 204302)
```

## Idempotent - 幂等性

如果一个函数执行多次皆返回相同的结果，则它是幂等性的

``` python
abs(abs(abs(10)))
# 10
```

## Point-Free Style - Point-Free 风格

定义函数时，不显式地指出函数所带参数，这种风格通常需要柯里化或者高阶函数

Point-Free风格的函数就像平常的赋值，不使用def或者lambda关键词

``` python
map_ = lambda func: lambda li: [func(l) for l in li]
add = lambda a: lambda b: a + b
increment_all = map_(add(1))

numbers = [1, 2, 3]
increment_all(numbers)
# [2, 3, 4]
```

## Predicate - 谓词

根据输入返回 True 或 False。常用于filter函数中

filter函数亦可以用列表推导式的if判断实现

``` python
above_two =  lambda a: a > 2
li = [1, 2, 3, 4]
[l for l in li if above_two(l)]
# [3, 4]
```

## Contracts - 契约

契约保证了函数或者表达式在运行时的行为。当违反契约时，将抛出一个错误。

``` python
def contract(input):
    if isinstance(input, int):
        return True
    raise Exception('Contract Violated: expected int -> int')

add_one = lambda num: contract(num) and num + 1
add_one(2)
# 3
add_one('hello')
# Exception Traceback
```

## Functor - 函子

一个实现了map函数的对象，map会遍历对象中的每个值并生成一个新的对象。

Python中最具代表性的函子就是list, 因为它遵守因子的两个准则

**在Python中可以用列表推导式来代表map操作**

### Preserves identity - 一致性

``` python
li = [1, 2, 3]
[l for l in li] == li
# True
```

### Composable - 组合性

``` python
li = [1, 2, 3]
compose = lambda f, g: lambda a: f(g(a))
[compose(str, lambda x: x+1)(l) for l in li]
# ['2', '3', '4']
[str(l+1) for l in li]
# ['2', '3', '4']
```

## Referential Transparency - 引用透明性

一个表达式能够被它的值替代而不改变程序的行为成为引用透明

``` python
greet = lambda: 'hello, world.'
```

## Lazy evaluation - 惰性求值

按需求值机制，只有当需要计算所得值时才会计算

Python中可用生成器实现

``` python
import random
def rand():
    while True:
        yield random.random()
rand_iter = rand()
next(rand())
# 0.16066473752585098
```

## Monoid - 单位半群

一个对象拥有一个函数用来连接相同类型的对象

数值加法是一个简单的Monoid

``` python
1 + 1
# 2
```

以上例子中，数值是对象，而+是函数

以下能更清晰地说明它

``` python
from operator import add
type(1)
# <class 'int'>
add(1, 1)
# 2
```

数值是int类的实例对象，add是实现了加法的函数

与另一个值结合而不会改变它的值必须存在，称为`identity`。

加法的identity值为 0:

``` python
1 + 0
# 1
```

需要满足结合律

``` python
1 + (2 + 3) == (1 + 2) + 3
# True
```

list的结合也是Monoid

``` python
[1, 2].extend([3, 4])
```

identity值为空数组

``` python
[1, 2].extend([])
```

identity与compose函数能够组成monoid

``` python
identity = lambda a: a
compose = lambda f, g: lambda a: f(g(a))
foo = lambda bar: bar + 1
compose(foo, identity)(1) == compose(identity, foo)(1) == foo(1)
# True
```

## Monad - 单子

拥有`of`和`chain`函数的对象。`chain`很像`map`，除了用来铺平嵌套数据。

``` python
flatten = lambda li: sum(li, [])
of = lambda *args: list(args)
chain = lambda func: lambda li: list(flatten([func(l) for l in li]))

[s.split(',') for s in of('cat,dog', 'fish,bird')]
# [['cat', 'dog'], ['fish', 'bird']]

chain(lambda s: s.split(','))(of('cat,dog', 'fish,bird'))
# ['cat', 'dog', 'fish', 'bird']
```

## Comonad - 余单子

拥有`extract`与`extend`函数的对象。

``` python
class CoIdentity:
    def __init__(self, v):
        self.val = v
    def extract(self):
        return self.val
    def extend(self, func):
        return CoIdentity(func(self))

CoIdentity(1).extract()
1
from beeprint import pp
pp(CoIdentity(1).extend(lambda x: x.extract() + 1))
# instance(CoIdentity):
#   val: 2
```

## Morphism - 态射

一个变形的函数

### Endomorphism - 自同态

输入输出是相同类型的函数

``` python
uppercase = lambda string: string.upper()
uppercase('hello')
# 'HELLO'

decrement = lambda number: number - 1
decrement(2)
# 1
```

### Isomorphism - 同构

不同类型对象的变形，保持结构并且不丢失数据。

例如，一个二维坐标既可以表示为列表`[2, 3]`，也可以表示为字典`{'x': 2, 'y': 3}`。

``` python
pair_to_coords = lambda pair: {'x': pair[0], 'y': pair[1]}
coords_to_pair = lambda coords: [coords['x'], coords['y']]
pair_to_coords(coords_to_pair({'x': 1, 'y': 2}))
#{'x': 1, 'y': 2}
```

## Setoid

拥有`equals`函数的对象。`equals`可以用来和其它对象比较。

Python里的`==`就是`equals`函数

``` python
[1, 2] == [1, 2]
# True

[1, 2] == [3, 4]
# False
```

## Semigroup - 半群

拥有`concat`函数的对象。`concat`可以连接相同类型的两个对象。

Python里列表的`extend`就是`concat`函数

``` python
li = [1]
li.extend([2])
li
# [1, 2]
```

## Foldable

一个拥有`reduce`函数的对象。`reduce`可以把一种类型的对象转化为另一种类型。

``` python
from functools import reduce
sum_ = lambda li: reduce(lambda acc, val: acc + val, li, 0)
sum_([1, 2, 3])
6
```

## Type Signatures - 类型签名

通常可以在注释中指出参数与返回值的类型

``` python
# add :: int -> int -> int
add = lambda x: lambda y: x + y

# increment :: int -> int
increment = lambda x: x + 1
```

如果函数的参数也是函数，那么这个函数需要用括号括起来

``` python
# call :: (a -> b) -> a -> b
call = lambda func: lambda x: func(x)
```

字符a, b, c, d表明参数可以是任意类型。以下版本的`map`的参数func，把一种类型a的数组转化为另一种类型b的数组

``` python
# map :: (a -> b) -> [a] -> [b]
map_ = lambda func: lambda li: [func(l) for l in li]
```

## 常用库

- [functools](https://docs.python.org/zh-cn/3/library/functools.html)
- [itertools](https://docs.python.org/zh-cn/3/library/itertools.html)
- [operator](https://docs.python.org/zh-cn/3/library/operator.html)
- [more-itertools](https://github.com/erikrose/more-itertools)
- [toolz](https://github.com/pytoolz/toolz)
