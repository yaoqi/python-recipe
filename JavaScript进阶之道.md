Python和JavaScript在笔者看来是很相似的语言，本文归纳了JavaScript的各种tricks，相对于[之前的Python版](https://zhuanlan.zhihu.com/p/35219750)。

两篇文章都读完，有没有发现它们的目录结构是一个样的呢XD

# 基本

## 模板字符串

``` javascript
let name = 'alphardex'
`Ore wa ${name} desu, ${4 * 6} sai, gakusei desu.`
// "Ore wa  desu, 24 sai, gakusei desu."
```

## 三元运算符

``` javascript
// if(condition){
//     fuck
// } else {
//     shit
// }
(condition)? fuck: shit
```

## 字符串的拼接，反转与分割

``` javascript
let letters = ['h', 'e', 'l', 'l', 'o']
letters.join('')
// "hello"
letters.reverse()
// ["o", "l", "l", "e", "h"]
let name = 'nameless god'
name.split(' ')
// ['nameless', 'god']
```

## 判断元素的存在性

``` javascript
'fuck you'.includes('fuck')
// true
['bitch', 'whore'].includes('slut')
// false
'company' in {'title': 'SAO III', 'company': 'A1 Pictures'}
// true
```

# 函数

## 箭头函数

函数的简化写法，配合map、filter、sort等实现函数式编程

``` javascript
// function foo(parameters){
//     return expression
// }
let foo = (parameters) => expression
```

### map - 映射

``` javascript
let numbers = [1, 2, 3, 4, 5];
numbers.map(e=>e ** 2)
// [1, 4, 9, 16, 25]
```

### filter - 过滤

``` javascript
let values = [null, undefined, NaN, 0, '', true, 'alphardex', 666]
values.filter(e=>e)
// [true, "alphardex", 666]
```

### sort - 排序

``` javascript
let numbers = [4, 2, 5, 1, 3];
numbers.sort((a, b)=>b-a)
// [5, 4, 3, 2, 1]
```

### 其他骚操作

求1到100的和

``` javascript
[...Array(101).keys()].reduce((a, b)=>a+b)
// 5050
// 或者用lodash实现，写法简直跟Python一模一样
// _.sum(_.range(101))
```

扁平化数组

``` javascript
const flatten = (arr, depth=1) => arr.reduce((a, v)=>a.concat(depth>1 && Array.isArray(v)?flatten(v, depth-1):v), [])
let arr = [1, [2, 3, ['a', 'b', 4], 5], 6]
flatten(arr, 2)
// [1, 2, 3, "a", "b", 4, 5, 6]
// 或者用ES10新增的flat
// arr.flat(2)
```

## 偏函数

用于固定原函数的某些参数，在此基础上造出一个新的函数

``` javascript
let multiply = (a, b) => a*b
let double = a => multiply(a, 2)
multiply(3, 4)
// 12
double(4)
// 8
```

## 扩展运算符

### 数据结构的合并

``` javascript
let arr1 = ['a', 'b']
let arr2 = [1, 2]
[...arr1, ...arr2]
// ['a', 'b', 1, 2]
let obj1 = {'name': 'alphardex'}
let obj2 = {'age': 24}
{...obj1, ...obj2}
// {name: 'alphardex', age: 24}
```

### 函数参数的打包

``` javascript
let foo = (...args) => console.log(args)
foo(1, 2)
// [1, 2]
```

# 数据结构

## 数组

### 推导式

由于推导式暂时不在标准规范内，因此用高阶函数配合箭头函数代替

``` javascript
let even = [...Array(10).keys()].filter(e=>e%2!==1)
even
// [0, 2, 4, 6, 8]
```

### 同时迭代元素与其索引

相当于Python的enumerate

``` javascript
let li = ['a', 'b', 'c']
li.map((e, i)=>`${i+1}. ${e}`)
// ["1. a", "2. b", "3. c"]
```

### 元素的追加与连接

push在末尾追加元素，concat在末尾连接元素

``` javascript
let li = [1, 2, 3]
li.push([4, 5])
li
// [1, 2, 3, [4, 5]]
li.concat([4, 5])
li
// [1, 2, 3, [4, 5], 4, 5]
```

### 测试是否整体/部分满足条件

every测试所有元素是否都满足于某条件，some则是测试部分元素是否满足于某条件

``` javascript
[1, 2, 3, 4, 5].every(e=>e<20)
// true
[1, 3, 4, 5].some(e=>e%2===0)
// true
```

### 同时迭代2个以上的数组

相当于Python的zip

``` javascript
let subjects = ['nino', 'miku', 'itsuki']
let predicates = ['saikou', 'ore no yome', 'is sky']
subjects.map((e,i)=>`${e} ${predicates[i]}`)
// ["nino saikou", "miku ore no yome", "itsuki is sky"]
```

### 去重

利用集合的互异性，同时此法还保留了原先的顺序

``` javascript
let li = [3, 1, 2, 1, 3, 4, 5, 6]
[...new Set(li)]
// [3, 1, 2, 4, 5, 6]
```

### 解构赋值

最典型的例子就是2数交换

``` javascript
let [a, b] = [b, a]
```

用rest运算符可以获取剩余的元素

``` javascript
let [first, ...rest] = [1, 2, 3, 4]
first
// 1
rest
// [2, 3, 4]
```

## 对象

### 遍历

``` javascript
let obj = {name: "alphardex", age: 24}
Object.keys(obj)
// ["name", "age"]
Object.values(obj)
// ["alphardex", 24]
Object.entries(obj).map(([key, value])=>`${key}: ${value}`)
// ["name: alphardex", "age: 24"]
```

### 排序

``` javascript
let data = [{'rank': 2, 'author': 'beta'}, {'rank': 1, 'author': 'alpha'}]
data.sort((a, b)=>a.rank - b.rank)
// [{'rank': 1, 'author': 'alpha'}, {'rank': 2, 'author': 'beta'}]
```

### 反转

``` javascript
let obj = {name: 'alphardex', age: 24}
Object.fromEntries(Object.entries(obj).map(([key, value])=>[value, key]))
// {24: "age", alphardex: "name"}
// 或者用lodash实现
// _.invert(obj)
```

# 语言专属特性

待整理
