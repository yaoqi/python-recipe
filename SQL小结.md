最近把[《SQL基础教程》](https://book.douban.com/subject/27055712/)给读完了，比之前读的[《SQL必知必会》](https://book.douban.com/subject/24250054/)讲的更加详细易懂，很多当时不懂的概念也瞬间明朗了。

本文是对SQL的CRUD的小结，配合Github上的[quick-SQL-cheatsheet](https://github.com/enochtangg/quick-SQL-cheatsheet/blob/master/README_zh-hans.md)食用更佳。

掌握了SQL，ORM什么的就更不在话下了。

## Create -> INSERT

``` sql
INSERT INTO <table> ([cols]) VALUES (<vals>);
```

## Retrieve -> SELECT

``` sql
SELECT [DISTINCT] <expressions>
FROM <table>
WHERE <condition>
GROUP BY <col>
HAVING <condition>
ORDER BY <cols> [DESC]
LIMIT <val>;
```

DISTINCT：去重

FROM：从哪个数据表中选择

WHERE：过滤数据的条件

GROUP BY：按某字段对数据进行分组（可以把它想象成切分表的一把刀）

> 常用聚合函数：COUNT, SUM, AVG, MIN, MAX

HAVING：对分好组的数据进行过滤

ORDER BY：对数据进行排序（默认ASC升序，亦可DESC降序）

LIMIT：列数上限

### 视图

一般将频繁使用的SELECT语句保存为视图，不仅节省了存储空间，而且提高了编码效率

``` sql
CREATE VIEW <view>
AS
SELECT ...
```

### 子查询

即一次性视图，可以嵌套，但尽量要避免嵌套过多

一般与别名AS连用来增强可读性

标量子查询：仅返回一个值的子查询，可以用在需要单一值的地方

关联子查询：在细分的组内比较时用到，关键是子查询中WHERE语句的条件（同样可以想象成对表的切分）

### 谓词

返回值是布尔值（TRUE/FALSE）的函数，常用的有如下几个：

- LIKE：字符串的部分一致查询（\_代表任意一个字符，%代表任意字符）
- BETWEEN：范围查询（包含临界值）
- IS (NOT) NULL：判断是否为NULL
- IN：用来表示多个OR

### CASE表达式

``` sql
CASE
WHEN <expression> THEN <expression>
...
ELSE <expression>
END
```

好比编程语言里的switch语句

### 集合运算

#### 基于行

``` sql
SELECT ...
UNION|INTERSECT|EXCEPT [ALL]
SELECT ...
ORDER BY <cols> [DESC];
```

UNION、INTERSECT、EXCEPT：并、交、差

后面加上ALL能保留重复行

画维恩图就能一目了然

#### 基于列

联结就是将其他表的列添加过来，说白了就是从多张表中选数据

``` sql
SELECT <cols> FROM <table1>
INNER JOIN|LEFT JOIN|RIGHT JOIN|FULL JOIN <table2>
ON <table1.col> = <table2.col> -- 联结键
```

联结分为内联结和外联结

内联结：INNER JOIN，仅当匹配的内容两张表都存在时才显示出来

外联结：OUTER JOIN，结果表中保留联结条件左边LEFT或右边RIGHT或两边FULL的所有内容

还有一种自联结，也就是表与表自身联结，常用来查找局部不一致的列

如果一时不理解，就举个栗子吧，以下语句用来查找价格相等但名称不一样的商品

``` sql
SELECT DISTINCT P1.name, P1.price
FROM Products P1, Products P2
WHERE P1.price = P2.price
AND P1.name <> P2.name
```

## Update -> UPDATE

``` sql
UPDATE <table>
SET <col> = <expression>
WHERE <condition>;
```

## Delete -> DELETE

``` sql
DELETE FROM <table>
WHERE <condition>;
```
