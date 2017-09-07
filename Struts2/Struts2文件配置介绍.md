---
title: Struts2文件配置介绍
tags: struts2
grammar_cjkRuby: true
---
## structs.xml文件配置

**package标签**

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
	<constant name="struts.i18n.encoding" value="UTF-8"></constant>
	<constant name="struts.action.extension" value="action,,"></constant>
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