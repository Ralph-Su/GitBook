---
title : Spark学习（一）：filter算子
tags:
date: 2020-01-01 20:08:25
categories: spark-sql
---

# Syntaxes
```scala
filter(condition: Column): Dataset[T] // 对Column进行条件过滤
filter(conditionExpr: String): Dataset[T] //using SQL expression 
filter(func: T => Boolean): Dataset[T]
filter(func: FilterFunction[T]): Dataset[T]
```

# Practice
### filter() with Column condition
用=!=表达not equal.
```scala
// This import is needed to use the $-notation and '-notation
import spark.implicits._

df1.filter(col("name") === "suwenjin").show()
df1.filter(df1("name") === "suwenjin").show()
df1.filter($"name" === "suwenjin").show()
df1.filter('name === "suwenjin").show()
df1.filter('name =!= "suwenjin").show() // not equal
```
使用`$-notation`和`'-notation`时需要导入`implicits`

### filter() with SQL Expression
```scala
df1.filter("name == 'suwenjin'").show()
df1.filter("name = 'suwenjin'").show()
```
sql表达式里可以使用"="或者"==="来判断是否相等。

### Filter with Multiple Conditions
AND(&&), OR(||), and NOT(!)
```scala
df1.filter($"name" === "suwenjin" && $"age" === 12).show()
df1.filter($"name" === "suwenjin" || $"age" =!= 12).show()
```

# Github地址
[https://github.com/jwsmai/ScalaTools/blob/main/src/main/scala/spark/sql/DataframeFilterExample.scala](https://github.com/jwsmai/ScalaTools/blob/main/src/main/scala/spark/sql/DataframeFilterExample.scala)
