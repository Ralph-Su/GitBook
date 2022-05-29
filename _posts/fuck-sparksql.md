---
title : Spark踩坑（一）：maven依赖冲突
tags: 踩坑记录
categories: spark-sql
---

# 背景
原来项目中只有spark的maven依赖，新增flink依赖后，spark程序运行报错。
```bash
$ Caused by: java.lang.ClassNotFoundException: com.esotericsoftware.kryo.pool.KryoFactory
```
SparkSql原来依赖`com.esotericsoftware.kryo:kryo:jar:2.21`，但flink依赖中包含`com.esotericsoftware.kryo:kryo:jar:2.24.0`和
`com.esotericsoftware.kryo:kryo:jar:2.21`， 在多版本同时存在的情况下，Java类加载器加载到了高版本的 kryo。由于高低版本不兼容，所以SparkSql报错。
```xml
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-clients</artifactId>
            <version>1.15.0</version>
        </dependency>
```

# 排查过程

使用下面的命令排查冲突的依赖：

```bash
$ mvn dependency:tree -Dverbose -Dincludes=com.esotericsoftware.kryo

[INFO] 
[INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ ScalaTools ---
[INFO] groupId:ScalaTools:jar:1.0-SNAPSHOT
[INFO] \- org.apache.flink:flink-clients:jar:1.15.0:compile
[INFO]    +- org.apache.flink:flink-core:jar:1.15.0:compile
[INFO]    |  \- com.esotericsoftware.kryo:kryo:jar:2.24.0:compile
[INFO]    \- org.apache.flink:flink-java:jar:1.15.0:compile
[INFO]       \- com.twitter:chill-java:jar:0.7.6:compile
[INFO]          \- (com.esotericsoftware.kryo:kryo:jar:2.21:compile - omitted for conflict with 2.24.0)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

从上面的执行结果中可以发现`org.apache.flink:flink-clients`下有两个版本的`com.esotericsoftware.kryo`， 而maven选择了其中版本更高的那个。