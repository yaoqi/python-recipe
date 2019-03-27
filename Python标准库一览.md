想掌握Python标准库，读它的[官方文档](https://docs.python.org/3.6/library/index.html)很重要。本文并非此文档的复制版，而是对每一个库的一句话概括以及它的主要函数，由此用什么库心里就会有数了。

此文配合[Python进阶之道](https://zhuanlan.zhihu.com/p/35219750)食用更佳~

<!--more-->

## 文本处理

- string: 提供了字符集：ascii\_lowercase, ascii\_uppercase, digits, hexdigits, punctuation
- re: 正则表达式支持(pattern, string)：match, search, findall, sub, split, finditer

## 数据结构

- datetime: 处理日期，建议用[arrow](https://github.com/crsmithdev/arrow)代替
- calendar: 日历: Calendar
- collections: 其他数据结构: deque, Counter, defaultdict, namedtuple
- heapq: 堆排序实现: nlargest, nsmallest, merge
- bisect: 二分查找实现: bisect, insort
- array: 数列实现: array
- copy: 浅拷贝和深拷贝: copy, deepcopy
- pprint: 漂亮地输出文本: pprint
- enum: 枚举类的实现: Enum

## 数学

- math: 数学函数库，函数太多故不一一列举
- fractions: 分数运算: Fraction as F
- random: 随机数: choice, randint, randrange, sample, shuffle, gauss
- statistics: 统计学函数: mean, median, mode, variance

## 函数式编程

- itertools: 迭代器工具: permutations, combinations, product, chain, repeat, cycle, accumulate
- functools: 函数工具: @wraps, reduce, partial, @lrucache, @singledispatch
- operator: 基本运算符

## 文件目录访问

- pathlib: 路径对象: Path
- fileinput: 读取一个或多个文件并处理行: input
- filecmp: 比较两个文件是否相同: cmp
- linecache: 读取文件的行，缓存优化: getline, getlines
- shutil: 文件操作: copy, copytree, rmtree, move, make\_archive

## 数据持久化

- pickle: 文件pickle序列化: dump, dumps, load, loads
- sqlite3: sqlite数据库接口

## 文件格式

- csv: 处理csv文件: reader, writeheader, writerow
- configparser: 处理配置文件: ConfigParser, get, sections

## 密码学

hashlib: 哈希加密算法: sha256, hexdigest

## 操作系统

- os: 操作系统，具体看文档
- io: 在内存中读写str和bytes: StringIO, BytesIO, write, get_value
- time: 计时器: time, sleep, strftime
- argparse, getopt: 命令行处理，建议用[click](https://github.com/pallets/click)或[docopt](https://github.com/docopt/docopt)代替
- logging: 打日志: debug, info, warning, error, critical，建议用[loguru](https://github.com/Delgan/loguru)代替
- getpass: 获取用户输入的密码: getpass
- platform: 提供跨平台支持: uname, system

## 并发执行

- threading: 多线程模型: Thread, start, join
- multiprocessing: 多进程模型: Pool, map, Process
- concurrent.futures: 异步执行模型: ThreadPoolExecutor, ProcessPoolExecutor
- subprocess: 子进程管理: run
- sched: 调度工具，建议用[schedule](https://github.com/dbader/schedule)代替
- queue: 同步队列: Queue

## 进程间通信和网络

- socket: 套接字，常用于服务器开发
- ssl: HTTPS访问
- select, selector: 多路复用I/O
- asyncio: 异步IO, eventloop, 协程: get\_event\_loop, run\_until\_complete, wait, async, await

## 网络数据处理

- email: 处理email
- json: 处理json: dumps, loads
- base64: 处理base64编码: b64encode, b64decode

## 结构化标记语言工具

- html: 转义html: escape, unescape
- html.parser: 解析html

## 网络协议支持

- webbrowser: 打开浏览器: open
- wsgiref: 实现WSGI接口
- uuid: 通用唯一识别码: uuid1, uuid3, uuid4, uuid5
- ftplib, poplib, imaplib, nntplib, smptlib, telnetlib: 实现各种网络协议

其余库用requests代替

## 程序框架

- turtle: 画图工具
- cmd: 实现交互式shell
- shlex: 利用shell的语法分割字符串: split

## GUI

tkinter：可用[pysimplegui](https://github.com/MikeTheWatchGuy/PySimpleGUI)代替，超好用

## 开发工具

- typing: 类型注解，可配合[mypy](https://github.com/python/mypy)对项目进行静态类型检查
- pydoc: 查阅模块文档: python -m pydoc [name]
- doctest: 文档测试: python -m doctest [pyfile]
- unittest: 单元测试: python -m unittest [pyfile]

## DEBUG和性能优化

- pdb: Python Debugger: python -m pdb [pyfile]
- cProfile: 分析程序性能: python -m cProfile [pyfile]
- timeit: 检测代码运行时间: python -m timeit [pyfile]

## 软件打包发布

- setuptools: 编写setup.py专用: setup, find\_packages
- venv: 创建虚拟环境，建议用[pipenv](https://github.com/pypa/pipenv)代替

## Python运行时服务

- sys: 系统环境交互: argv, path, exit, stderr, stdin, stdout
- builtins: 所有的内置函数和类，默认引进（还有一个[boltons](https://github.com/mahmoud/boltons)扩充了许多有用的函数和类）
- \_\_main\_\_: 顶层运行环境，使得python文件既可以独立运行，也可以当做模块导入到其他文件。
- warnings: 警告功能（代码过时等）: warn
- contextlib: 上下文管理器实现: @contextmanager

## 自定义Python解释器

code: 实现自定义的Python解释器（比如Scrapy的shell）: interact

## 未提及的库

difflib, textwrap, unicodedata, stringprep, rlcompleter, struct, codecs, weakref, types, reprlib, numbers, cmath, decimal, stat, tempfile, fnmatch, macpath, copyreg, shelve, marshal, zlib, gzip, bz2, lzma, zipfile, tarfile, netrc, xdrlib, plistlib, audioop, aifc, sunau, wave, chunk, colorsys, imghdr, sndhdr, bdb, faulthandler, trace, tracemalloc, abc, atexit, gc, inspect, site, hmac, secrets, signal, mmap, mailcap, mailbox, mimetypes, binhex, binascii, quopri, uu, errn, traceback, ctypes

未提及的理由：这些库笔者没怎么用过，或者本身就不怎么常用，如果觉得有用的话可以自行查看它们的官方文档。
