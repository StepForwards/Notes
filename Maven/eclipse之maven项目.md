---
title: eclipse之maven项目
tags: eclipsp,maven
grammar_cjkRuby: true
---
## maven项目

 - eclipse需要安装m2e插件，我们eclipse中已经集成m2e插件，因此不需要下载m2e插件
 - eclipse中默认带了maven管理，我们可以绑定自己下载的版本进行相关操作

![绑定新的maven][1]

 - 加载默认的配置文件，从新的配置文件中加载

![绑定新的配置文件][2]

 - 新建maven项目

![新建maven项目][3]

 - 跳过骨架操作

![选择跳过骨架操作][4]

如果不跳过骨架，创建的工程的目录是不完整的

 - 设置坐标和打包方式
	 - jar表示java工程
	 - war表示web工程
	 - pom表示父工程（分模块开发的时候需要）
	 
![配置坐标信息][5]

 - 默认没有web.xml文件需要进行添加web.xml文件

![添加web.xml][6]

 - 建立索引

![找到仓库][7]

![建立索引][8]

 - 创建servlet
 - 添加jar的依赖 ==servlet-api== 和 ==jsp-api==

## maven的依赖管理

> 通过maven的依赖管理对项目中的jar包进行统一管理

 - ==\<dependencies>== 标签表示当前项目所依赖的jar包，其内部是 ==\<dependency>== 标签
 - ==\<dependency>== 表示具体的依赖其内部有4个标签
 - ==\<groupId>== 表示组织名+项目名
 - ==\<artifactId>== 模块名称
 - ==\<version>== 表示版本号
 - ==\<scope>== 表示依赖范围
	 - ==compile== 表示编译时依赖，是默认值，会在编译，测试，运行的时候使用
	 - ==provided== 只在编译和测试时依赖，运行的时候不依赖，如 ==servlet-api== 因为tomcat已经提供，所以运行的时候如果使用就会出现错误，所以设置为 ==provided==
	 - ==runtime== 运行和测试的时候要，编译的时候不需要，如jdbc驱动设置为runtime
	 - ==test== 测试的时候依赖，编译和运行的时候不依赖，如Junit
	 - ==system== 依赖范围和provided相同，但是需要提供一个磁盘路径，不推荐使用

|依赖范围|对于编译classpath有效|对于测试classpath有效|对于运行时classpath有效|例子|
|-|-|-|-|-|
|compile|Y|Y|Y|spring-core|
|test|-|Y|-|Junit|
|provided|Y|Y|-|servlet-api|
|runtime|-|Y|Y|JDBC驱动|
|system|Y|Y|-|本地的，Maven仓库之外的类库|
[scope依赖范围]

``` xml
<!-- 加入ServletAPI -->
		<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.3</version>
			<scope>provided</scope>
		</dependency>
```

## maven的坐标查找

### 网页查找

> 如果我们需要使用jar包，就需要知道对应的jar包的坐标，可以通过[https://mvnrepository.com/][9]进行查找

![通过网页查找JUnit][10]

![JUnit][11]

![JUnit4.12][12]

![JUnit4.12详情][13]

### 搜索本地仓库

> 本地搜索需要先构建索引，前面已经介绍

![搜索本地][14]

![搜索详情][15]

### 配置插件

> maven中也可以配置很多插件，如jdk版本和tomcat的插件

 - ==\<build>== 项目构建配置，配置编译，运行插件等
 - ==\<plugins>== 内部放 ==\<plugin>== ,是管理的插件
 - ==\<plugin>== 插件标签，内部配置该插件内部有 ==\<configuration>==
 - ==\<configuration>== 表示配置信息
 - 处理编译器版本

``` xml
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-compiler-plugin</artifactId>
			<version>3.5.1</version>
			<configuration>
				<source>1.7</source>
				<target>1.7</target>
				<encoding>UTF-8</encoding>
			</configuration>
		</plugin>
	</plugins>
</build>
```


 - tomcat插件

``` xml
<build>
		<plugins>
			<plugin>
				<groupId>org.apache.tomcat.maven</groupId>
				<artifactId>tomcat7-maven-plugin</artifactId>
				<version>2.2</version>
			</plugin>
		</plugins>
	</build>
```


  [1]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502977151528.jpg
  [2]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502978732742.jpg
  [3]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502978872606.jpg
  [4]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502978999531.jpg
  [5]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502979125813.jpg
  [6]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502979750201.jpg
  [7]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502979815910.jpg
  [8]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502979956028.jpg
  [9]: https://mvnrepository.com/
  [10]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502981854332.jpg
  [11]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502981901384.jpg
  [12]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502981948204.jpg
  [13]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502981991912.jpg
  [14]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502982122168.jpg
  [15]: https://www.github.com/StepForwards/my-notes/raw/images/eclipse%E4%B9%8Bmaven%E9%A1%B9%E7%9B%AE/images/1502982178388.jpg