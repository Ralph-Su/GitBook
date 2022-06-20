---
title: SparkSQL数据处理方法
date: 2022-06-17 17:50:41
tags: sparksql
---

# Convert Struct to String
use `to_json()` function
```scala
 df.withColumn("_id", to_json($"_id"))
```

# Convert Array to String
use `concat_ws()` function
```scala
 df.withColumn("processed_results", concat_ws(",",$"processed_results"))
```

# Regexp
[https://www.modb.pro/db/43300#21_regexp_84](https://www.modb.pro/db/43300#21_regexp_84)