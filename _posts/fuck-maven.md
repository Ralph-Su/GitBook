---
title : Maven学习（一）：基本概念
date: 2020-05-29 20:08:25
tags: 
categories: maven
---
# jar 包的仓库
- maven 有两部分，首先是服务器端，叫做maven repo，或者nexusserver。 它将所有的 jar 包放在一个仓库里。
- 所有 jar 包都发布到这个仓库。需要用到某个jar 包，就去这个仓库下载。
- 仓库里每个 jar 包，都有唯一的 id。这个id 是由三部分组成的：groupid，artifact id 和 version。
group id是发布jar包的组织名称，artifact id是jar包的名称，version是版本号。
```xml
<dependency>
  <groupId>org.scalanlp</groupId>
  <artifactId>breeze_2.10</artifactId> <!-- or 2.11 -->
  <version>0.10</version>
</dependency>
```
- 为了避免每次都从服务器下载 artifact（jar包），maven会把下载好的artifact 放在本地的文件夹，这个就叫做local repo, local repor地址：
```shell
/Users/suwenjin/.m2
```

# maven客户端
- 如果一个项目 ChatRoom 依赖某个 jar 包，比如guava，那么就把guava的 id 加入到自己的依赖里，maven 客户端就可以通过id找到并使用guava 了。
- 同时，maven 的依赖是传递的。如果使用maven 发布这个jar包到maven repo，maven 还会记住ChatRoom的jar 包依赖于guava。如果有别的项目依赖 ChatRoom，那么它将自动依赖guava，无需再次声明。

# 命令
- maven 构建中的几个主要的 phase：clean compile test package install site
  1. clean: 清楚maven之前生成的class、jar等文件
  2. compile:将java文件编译成class文件
  3. test: 执行测试用例
  4. package: 将项目打成jar包
  5. install: 将jar包安装到local repo
  6. site: 生成项目相关信息的网站
  
- 其他命令：
  1. mvn dependency:resolve 打印出已解决依赖的列表
  2. mvn dependency:tree 打印整个依赖树
  3. mvn install -X 想要查看完整的依赖踪迹，包含那些因为冲突或者其它原因而被拒绝引入的构件，打开 Maven 的调试标记运行

# 作用域
[Maven的依赖(1) 之 依赖的作用域scope](https://www.jianshu.com/p/c5d84c2c7fc8)
# 插件
- 插件是什么：maven 其实是一套框架，所有的具体任务都是插件完成的。除了核心的编译打包插件，还有非常多的别的目的的插件。
- 打出 fatjar 的插件