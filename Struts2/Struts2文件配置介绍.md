---
title: Struts2文件配置介绍
tags: struts2
grammar_cjkRuby: true
---

-	* [structs.xml文件配置](#structsxml文件配置)
		* [标签](#标签)
		* [常量配置](#常量配置)
		* [动态方法调用](#动态方法调用)
		* [structs2中的默认配置](#structs2中的默认配置)
		* [结果跳转的方式](#结果跳转的方式)

## structs.xml文件配置

### 标签

**package标签**

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
	<!-- 配置post请求以及repsone的编码格式 -->
	<constant name="struts.i18n.encoding" value="UTF-8"></constant>
	<!-- 配置请求路径的扩展名 -->
	<constant name="struts.action.extension" value="action,,"></constant>
	<!-- 开启热部署 -->
	<constant name="struts.devMode" value="true"></constant>
	
	<package name="index" namespace="/" extends="struts-default">
		<action name="" class="com.forward.test.web.action.UserAction" method="toLogin">
			<result name="toLogin">/WEB-INF/view/login.jsp</result>
		</action>
	</package>
	
	<include file="com/forward/test/web/action/struts.xml"></include>
</struts>
```

> <package> 配置web应用的不同模块,一般在一个功能模块下配置一个package,在当前的package下配置这个模块的多个action

- `name`属性给不同的模块起不同的名字,随便写,不重复即可
- `namespace`属性给不同的模块设置访问的根路径,可以配置成/
- `extends`属性表示继承, `struts-default` 是struts2给我们提供的一个package

**action标签**

> `action` 标签表示配置一个请求

- `name` 属性表示请求路径的后缀,一般表示功能模块中的具体请求,name的名字就代
表访问路径的名称
- `class` 属性表示当有请求过来的时候调用的是哪个类中的方法,配置全类名
- `method` 表示class 请求调用的是class 中的哪个方法,指的是具体的方法名

**result标签**

> result 结果配置,用于设置不同的方法返回值,可以配置不同的返回值对应不同的视图

- `name` 属性表示结果处理名称,与action中的返回值对应
- `type` 属性表示指定哪个result 类来处理显示的页面,默认是内部转发,可以
在struts-default 的文件中进行查看
- `标签体`表示相对路径,相对于web应用开始

### 常量配置

> 默认的常量配置在structs核心包中
> ![默认常量配置][1]

**修改常量配置方式及加载顺序**

> 对于常量的配置, 默认加载的是structs核心包中的default.properties,如果通过以下3种进行配置,就会按照默认-->1-->2-->3 的顺序加载,后面设置的常量会覆盖之前设置的常量

1. 在structs.xml文件中,在structs的根标签下,书写constant 标签进行配置,在项目中主要使用这种方式
2. 在src下创建structs.properties文件,将内容复制到此文件进行修改
3. 在web.xml文件中,配置context-param

![第一种方式][2]

![第二种方式][3]

![第三种方式][4]

**常用常量设置**

- struts.i18n.encoding=UTF-8 用于配置接收参数和向外输出中文的编码格式一般设置为UTF-8
- struts.action.extension=action,, 指定访问action的路径的后缀名,使用, 表示可以有两个后缀名,可以是action也可以是没有后缀名
- struts.devMode = false 指定structs是否是以开发模式运行,能够支持修改配置文件后进行热部署,所以我们可以将其设置为true

### 动态方法调用

> 如果一个业务模块有多个方法,我们可以使用动态方法调用省略action的配置,设置动态方法调用有两种方法

- 方法一
	- 开启动态方法调用`<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>`
	- 配置action的时候不写method
	- 在访问的时候输入网址`http://localhost:8080/webapp/namespace/name!method`
``` xml
	<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>
	<package name="helloWorld" namespace="/User" extends="struts-default">
		<action name="d_" class="com.zhiyou100.struts.web.action.demo3.Demo3Action" >
			<result name="success">/hello World.jsp</result>
		</action>
	</package>
```
- 方法二 通配符方式
	- 关闭动态方法调用
	- 对于方法名可以使用一个* 通配符,在后面的class和method可以使用{索引} 来读取前面的内容
	- 访问路径localhost:8080/webapp/namespace/class_method

``` xml
	<package name="demo3" namespace="/User" extends="struts-default">
		<action name="*_*" class="com.zhiyou100.struts.web.action.demo3.{1}" method="{2}">
			<result name="success">/helloWorld.jsp</result>
		</action>
	</package>
```
### structs2中的默认配置
- `method`的默认值`execute`
- `result`的默认值是`success`
- `result`的type的默认值是`dispatcher`
- `class`的默认值是`ActionSupport` 其中有`execute` 方法返回值是`success`
- 配置`package`下的默认的`action`,当访问当前包下,如果找不到指定`action`,就会自动寻找默认的`action`

``` xml
	<package name="default" namespace="/user" extends="struts-default">
		<default-action-ref name="demoAction"></default-action-ref>
		<action name="demoAction" class="com.forward.test.web.action.UserAction">
			<result>/WEB-INF/view/404.jsp</result>
		</action>
	</package>
```
### 结果跳转的方式

> 结果的跳转方式可以通过result的type属性进行设置

**转发**

转发到指定页面

> 对于`type`属性,默认是`dispatcher` ,就是转发到响应界面,可以不用进行配置

转发到指定action

> 对于`type`属性需要设置为`chain` ,并在其下方配置`<param>` 标签

``` xml
			<result name="error" type="chain">
				<param name="namespace">/</param>
				<param name="actionName"></param>
			</result>
```
**重定向**

重定向到指定界面

> 对于type属性,设置为`redirect` ,就是重定向到界面,如果需要进行重定向就必须进行此处的设置

``` xml
			<result name="error" type="redirectAction">
				<param name="namespace">/</param>
				<param name="actionName"></param>
			</result>
```







  [1]: https://www.github.com/StepForwards/my-notes/raw/images/Struts2%E6%96%87%E4%BB%B6%E9%85%8D%E7%BD%AE%E4%BB%8B%E7%BB%8D/images/1504786924863.jpg
  [2]: https://www.github.com/StepForwards/my-notes/raw/images/Struts2%E6%96%87%E4%BB%B6%E9%85%8D%E7%BD%AE%E4%BB%8B%E7%BB%8D/images/1504787349513.jpg
  [3]: https://www.github.com/StepForwards/my-notes/raw/images/Struts2%E6%96%87%E4%BB%B6%E9%85%8D%E7%BD%AE%E4%BB%8B%E7%BB%8D/images/1504787419291.jpg
  [4]: https://www.github.com/StepForwards/my-notes/raw/images/Struts2%E6%96%87%E4%BB%B6%E9%85%8D%E7%BD%AE%E4%BB%8B%E7%BB%8D/images/1504787483194.jpg