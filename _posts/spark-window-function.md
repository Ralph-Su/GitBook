---
title : Spark学习（二）：窗口函数
tags: 窗口函数
categories: spark-sql
---

# Syntaxes
```scala
import org.apache.spark.sql.expressions.Window
val window = Window.partitionBy("columnName")
df.withColumn("newColumn", sum("xxx").over(window))
```
按照上面的写法就是根据某一列开窗，**当我们不需要对某一列开窗的时候而对所有行进行sum，我们不对`.over`函数传参即可**。

# Practics
```scala
case class Salary(depName: String, empNo: Long, salary: Long)

  val empsalary = Seq(
    Salary("sales", 1, 50),
    Salary("personnel", 2, 39),
    Salary("sales", 3, 48),
    Salary("personnel", 5, 35),
    Salary("develop", 7, 42),
    Salary("develop", 11, 52)).toDF()

  // 窗口函数
  val byDepName = Window.partitionBy("depName")
  empsalary.withColumn("depSalSum", sum("salary").over(byDepName))
    .withColumn("depSalMax", max("salary").over(byDepName))
    .withColumn("depSalMin", min("salary").over(byDepName))
    .withColumn("AllSum", sum("salary").over()) // 直接sum所有列
    .show()
```
# Github地址
[https://github.com/jwsmai/ScalaTools/blob/main/src/main/scala/spark/sql/WindowFunctionExample.scala](https://github.com/jwsmai/ScalaTools/blob/main/src/main/scala/spark/sql/WindowFunctionExample.scala)
# 参考文章
[https://medium.com/expedia-group-tech/deep-dive-into-apache-spark-window-functions-7b4e39ad3c86](https://medium.com/expedia-group-tech/deep-dive-into-apache-spark-window-functions-7b4e39ad3c86)