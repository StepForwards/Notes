---
title: Maven 
tags: Maven
grammar_cjkRuby: true
---

[toc]

## Maven应用配置

> maven是apache下的一个开源项目，其功能是为了管理java工程，在项目中主要被我们用来管理jar包

## 下载安装

 - 下载地址：[maven][1]
 - 解压即安装，注意解压到一个没有中文和空格的目录

![maven的目录结构][2]

 -  配置环境变量
	 -  电脑上需要安装jdk1.7以上的jdk，需要配置JAVA_HOME
	 -  配置MAVEN_HOME
	 
	 ![maven_home][3]
	 
 - 将 ==%MAVEN_HOME%/bin== 配置到环境变量path中

	![path][4]
	
 - 在cmd窗口输入 ==mvn -v== 查看

	![安装成功][5]
	
## maven的仓库

> maven是将用到的所有jar包，保存到仓库中
> 仓库的分类：本地仓库，远程仓库，中央仓库

 - 本地由我们自己管理，存储在本地电脑中
 - 远程仓库（私服）部分公司会服务器中搭建公司内部的仓库
 - 中央仓库，由maven团队进行管理，维护着大概两个亿的jar包
 - 当需要的jar包本地仓库中没有时，会一级一级进行下载，如果没有远程仓库，本地仓库就会直接从中央仓库进行下载

## 配置本地仓库

 - 将仓库解压到
 
![仓库路径][6]

 - 打开maven中的conf文件夹，编辑 ==settings.xml==
 - 配置本地仓库路径为

```xml
<!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
	 <localRepository>D:\Develop\maven\repository</localRepository>
```
 - 在 ==<profiles></profiles>==标签中添加 ==jdk1.8== 的支持， ==默认是jdk1.5==

``` xml
<profiles>
<profile>  
    <id>jdk18</id>  
    <activation>  
        <activeByDefault>true</activeByDefault>  
        <jdk>1.8</jdk>  
    </activation>  
    <properties>  
        <maven.compiler.source>1.8</maven.compiler.source>  
        <maven.compiler.target>1.8</maven.compiler.target>  
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>  
    </properties>   
</profile> 
 </profiles>
```
## maven工程的目录结构

> maven的目录结构具有一定的规范

 - ==ser/main/java== 存放项目的 ==.java== 文件
 - ==src/main/resources== 存放项目的资源配置文件，如spring和mybatis中的 ==xml== 文件
 - ==ser/main/webapp== web工程存放 ==web== 资源
 - ==src/main/webapp/WEB-INF== 存放 ==web.xml== 文件
 - ==src/test/java== 存放单元测试的 ==.java== 文件，如JUnit测试类
 - ==ser/test/resouces== 测试的资源文件，如果没有会从main中寻找
 - ==target== 项目输出位置，编译后的==class==文件，打包成 ==jar== 和 ==war==
 - ==pom== maven项目的核心配置文件
 
 ## maven常用命令

> maven本身集成了很多插件，如jdk插件和tomcat插件；因此在没有开发工具的前提下对项目进行清理，编译，运行，打包等操作
	以下命令均在maven工程下
	
	![maven工程HelloWorld][7]
	
### compile编译命令

> 通过 ==mvn compile== 可以编译ser/main/java下的文件生成class文件输出到wargert文件夹，不会编译test中的文件![enter description here][8]

### clean命令

> 通过 ==mvn clean== 可以清除target中的文件

### package命令

> 通过 ==mvn package== 可以将web项目打包成war，java项目打包成jar，放在target目录下，使用此命令会执行 ==compile，test==

### install命令

> 通过 ==mvn install== 可以将对应的应用打包成war和jar并发布到本地仓库中，对于war包发布到本地仓库是没有用的，因此我们要进行依赖管理只会管理jar包，内部执行 ==compile，test，package==

### tomcat：run命令

> 通过 ==mvn tomcat：run== 可以使用内置的tomcat插件运行web应用，注意结束窗口使用 ==ctrl+v==

![mvn tomcat:run][9]

### 命令同时执行

> 例如通过 ==mvn clean install== 同时执行两个命令

  [1]: http://maven.apache.org/download.cgi
  [2]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502970304702.jpg
  [3]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502970616082.jpg
  [4]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502970851690.jpg
  [5]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502971060857.jpg
  [6]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502971938574.jpg
  [7]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502973894207.jpg
  [8]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502974496054.jpg
  [9]: https://www.github.com/StepForwards/my-notes/raw/images/Maven/images/1502975554940.jpg