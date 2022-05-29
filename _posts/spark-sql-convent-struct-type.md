---
title : Spark学习（四）：dataframe构造structType
tags: 
categories: spark-sql
---
# 需求背景
为满足业务系统能够像解析mongo数据一样解析大数据产出的数据，我们绝对将多个字段的多行数据处理到一个字段中。
如下表中的数据所示，一组task和serid有多个sku，一个sku下又有多个location,我们需要将这些数据处理成嵌套形式的Schema。

# 示例数据
```md
+-------+---------+------+--------+----+----+----+----+
|task_id|serial_id|sku_id|sku_name|xMax|xMin|Ymin|Ymax|
+-------+---------+------+--------+----+----+----+----+
|      a|        1|     1|    啤酒|   0|   0|   0|   0|
|      a|        1|     1|    啤酒|   0|   0|   0|   1|
|      a|        1|     1|    啤酒|   0|   0|   1|   0|
|      a|        1|     1|    啤酒|   0|   0|   1|   1|
|      a|        1|     2|    可乐|   0|   1|   0|   0|
|      a|        1|     2|    可乐|   0|   1|   0|   1|
|      a|        1|     2|    可乐|   0|   1|   1|   0|
|      a|        1|     2|    可乐|   0|   1|   1|   1|
+-------+---------+------+--------+----+----+----+----+
```
# 示例数据Schema
```
root
|-- task_id: string (nullable = true)
|-- serial_id: integer (nullable = true)
|-- sku_id: integer (nullable = true)
|-- sku_name: string (nullable = true)
|-- xMax: integer (nullable = true)
|-- xMin: integer (nullable = true)
|-- Ymin: integer (nullable = true)
|-- Ymax: integer (nullable = true)
```
# 目标数据Schema
```scala
root
|-- task_id: string (nullable = true)
|-- serial_id: integer (nullable = true)
|-- processed_results: array (nullable = false)
|    |-- element: struct (containsNull = false)
|    |    |-- bounding_box: struct (nullable = false)
|    |    |    |-- top_left: struct (nullable = false)
|    |    |    |    |-- Xmin: integer (nullable = true)
|    |    |    |    |-- Ymin: integer (nullable = true)
|    |    |    |-- bottom_right: struct (nullable = false)
|    |    |    |    |-- Xmax: integer (nullable = true)
|    |    |    |    |-- Ymax: integer (nullable = true)
|    |    |-- sku_lists: struct (nullable = false)
|    |    |    |-- sku_id: integer (nullable = true)
|    |    |    |-- sku_name: string (nullable = true)
```
# 关键代码
```scala
 val structDF = df.groupBy($"task_id", $"serial_id")
    .agg(
      collect_list(
        struct(
          struct(struct($"Xmin", $"Ymin").as("top_left"), struct($"Xmax", $"Ymax").as("bottom_right")).as("bounding_box"),
          struct($"sku_id", $"sku_name").as("sku_lists")
        )
      ).as("processed_results")
    )
```


