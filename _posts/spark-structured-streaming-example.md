---
title : Spark学习（三）：Watermark
tags: 实时计算
categories: spark-structured-streaming
---
# 程序配置
窗口大小：10 minutes
watermark大小：10 minutes
滑动时间：5 minutes
触发方式：default, 尽快触发, 自由切割

# 测试数据
| event_time          | word | isCounted | watermark           | window of watermark                       |
|---------------------|------|-----------|---------------------|-------------------------------------------| 
| 2022-05-25 09:45:00 | a    | count     | 2022-09-25 09:35:00 | 2022-09-25 09:30:00 ~ 2022-09-25 09:40:00 |
| 2022-05-25 09:25:00 | b    | not count | 2022-09-25 09:35:00 | 2022-09-25 09:30:00 ~ 2022-09-25 09:40:00 |
| 2022-05-25 09:29:59 | b    | not count | 2022-09-25 09:35:00 | 2022-09-25 09:30:00 ~ 2022-09-25 09:40:00 |
| 2022-05-25 09:30:00 | b    | count     | 2022-09-25 09:35:00 | 2022-09-25 09:30:00 ~ 2022-09-25 09:40:00 |
| 2022-05-25 09:35:00 | b    | count     | 2022-09-25 09:35:00 | 2022-09-25 09:30:00 ~ 2022-09-25 09:40:00 |
| 2022-05-25 09:50:00 | c    | count     | 2022-09-25 09:40:00 | 2022-09-25 09:35:00 ~ 2022-09-25 09:45:00 |
| 2022-05-25 09:30:00 | d    | not count |  2022-09-25 09:40:00 | 2022-09-25 09:35:00 ~ 2022-09-25 09:45:00 |
| 2022-05-25 09:34:00 | d    | not count |  2022-09-25 09:40:00 | 2022-09-25 09:35:00 ~ 2022-09-25 09:45:00 |
| 2022-05-25 09:35:00 | d    | count     |  2022-09-25 09:40:00 | 2022-09-25 09:35:00 ~ 2022-09-25 09:45:00 |
| 2022-05-25 09:35:01 | d    | count     |  2022-09-25 09:40:00 | 2022-09-25 09:35:00 ~ 2022-09-25 09:45:00 |

# 结论
1. 如果数据的event_time >= watermark所在最近的一个window的开始时间，就会被处理。而不是>=watermark的时间。
2. watermark属于多个窗口时，用于比较的是时间最靠后的一个窗口。比如2022-09-25 09:35:00所在窗口是09：25～09：35，09：30～09：40，spark是用第二个窗口来比较event_time。

# 代码
```scala

  // 设置需要监听的本机地址与端口号
  val host: String = "127.0.0.1"
  val port: String = "9999"

  //  nc -lk 9999

  // 从监听地址创建DataFrame
  var df: DataFrame = spark.readStream
    .format("socket")
    .option("host", host)
    .option("port", port)
    .load()


  /**
   * 使用DataFrame API完成Word Count计算
   * */
  // 首先把接收到的字符串，以空格为分隔符做拆分，得到单词数组words
  val countDf = df.withColumn("inputs", split(df("value"), ","))
    .withColumn("event_time", element_at(col("inputs"), 1).cast("timestamp"))
    .withColumn("word", element_at(col("inputs"), 2))
    // 根据event_time计算watermark
    .withWatermark("event_time", "10 minutes")
    // 10 mins windows, sliding every 5mins
    .groupBy(window(col("event_time"), "10 minutes", "5 minutes"), col("word"))
    .count()

  countDf.writeStream
    .format("console") // 指定Sink为终端（Console）
    .option("truncate", false) // 指定输出选项,“truncate”选项，用来表明输出内容是否需要截断。
//     .outputMode("complete")
    .outputMode("update")
    .start() // 启动流处理应用
    .awaitTermination() // 等待中断指令
```

# Github

[https://github.com/jwsmai/ScalaTools/blob/main/src/main/scala/spark/stream/WordCountWaterMarkWindowExample.scala](https://github.com/jwsmai/ScalaTools/blob/main/src/main/scala/spark/stream/WordCountWaterMarkWindowExample.scala)