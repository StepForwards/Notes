---
title: SpringMVC
tags: spring,springMVC
grammar_cjkRuby: true
---
[toc]
## SpringMVC

> 全名是Spring web mvc 是web层的框架，它是Spring框架的一部分

### SpringMVC处理流程

> 前端控制器（DispatcherServlet）是springmvc的心脏，用来拦截用户的请求，其本质是一个servlet，其收到请求后将数据交给处理器（handler）去执行相关代理

``` sequence
客户端->前端控制器(DispatcherServlet): 用户请求
前端控制器(DispatcherServlet)->处理器(handler):  请求业务处理
处理器(handler)-->前端控制器(DispatcherServlet): 返回处理结果
前端控制器(DispatcherServlet)->jsp页面: 处理结果给jsp
jsp页面-->前端控制器(DispatcherServlet): 返回HTML页面
前端控制器(DispatcherServlet)-->客户端: 响应用户
```
### Hello World

 - 导包 4+2+aop+web+webmvc+jstl(1.2版本只需一个文
件即可)
 - 设置springmvc的约束文件
 - 新建config的source folder
 - 创建配置文件springmvc.xml,配置扫描controller包

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://www.springframework.org/schema/beans" xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans-4.2.xsd 
	http://www.springframework.org/schema/aop 
	http://www.springframework.org/schema/aop/spring-aop-4.2.xsd 
	http://www.springframework.org/schema/context 
	http://www.springframework.org/schema/context/spring-context-4.2.xsd 
	http://www.springframework.org/schema/mvc 
	http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd ">
	
<context:component-scan base-package="com.forward.springmvc.controller"></context:component-scan>

</beans>
```
- 配置web.xml文件，配置前端控制器servlet的初始化参数为springmvc配置文件的路径，init-param的属性名和Spring容器的context-param属性名相同均为contextConfigLocation,只不过这个初始化参数指的是servlet的初始化参数（如果不配置路径，Springmvc会默认去找/WEB-INF/servlet名字-serlet.xml文件，如果没有就会报异常）

``` xml
<!-- 配置Spring核心文件 -->
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:spring/springmvc.xml</param-value>
		</init-param>
		<!-- <load-on-startup>1</load-on-startup> -->
	</servlet>
	<!-- 配置映射方式 -->
	<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
		<url-pattern>*.action</url-pattern>
	</servlet-mapping>
```

 - 创建一个包叫做controller 这个就是web层
 - 在其中创建类,用来处理请求,使用注解的形式将其配置
到spring容器中,在其中创建方法处理请求

``` java
package com.forward.springmvc.controller;

import java.sql.Timestamp;
import java.util.ArrayList;
import java.util.List;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import com.forward.springmvc.model.Role;

@Controller
public class RoleController {
	

	@RequestMapping("/role/role.action")
	public ModelAndView roleList(){
		ModelAndView mv = new ModelAndView();
		List<Role> list = new ArrayList<Role>();
		list.add(new Role(1, "张三", "ssd", new Timestamp(System.currentTimeMillis())));
		list.add(new Role(2, "张三", "ssd", new Timestamp(System.currentTimeMillis())));
		list.add(new Role(3, "张三", "ssd", new Timestamp(System.currentTimeMillis())));
		list.add(new Role(4, "张三", "ssd", new Timestamp(System.currentTimeMillis())));
		mv.addObject("list", list);
		mv.setViewName("/WEB-INF/view/role/roleList.jsp");
		return mv;
	}
}
```
![springmvc架构][1]

**1. Http请求**：客户端请求提交到DispatcherServlet。
**2.寻找处理器**：由DispatcherServlet控制器查询一个或多个HandlerMapping，找到处理请求的Controller。
**3. 调用处理器**：DispatcherServlet将请求提交到Controller。
**4. 5. 调用业务处理和返回结果**：Controller调用业务逻辑处理后，返回ModelAndView。
**6. 7. 处理视图映射并返回模型**：DispatcherServlet查询一个或多个ViewResoler视图解析器，找到ModelAndView指定的视图。
**8. Http响应**：视图负责将结果显示到客户端。

### SpringMVC组件介绍及其配置

 - DispatcherServlet：前端控制器
 
> 用户请求到达前端控制器，他就相当于mvc模式中的c，DispatcherServlet是整个流程控制中心，由它调用其它组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。

 - HandlerMapping：处理器映射器

> HandlerMapping负责根据用户请求url找到Handler既处理器，springmvc提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等，在controller中对应的方法使用的@RequestMapping就是注解的方式配置，在企业开发过程中也是目前使用最多的配置方式。

 - Handler：处理器

> Handler是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制器下Handler对具体的用户请求进行处理。
> 由于Handler涉及到具体的用户业务请求，所以一般情况需要程序员根据业务需求开发Handler。

 - HandlAdapter：处理器适配器

> 通过HandlerAdaptec对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

 - ViewResolver：视图解析器

> ViewResolver负责将处理结果生成View视图，ViewResolver首先根据逻辑视图名解析成物理视图名既具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。

 - View：视图

> springmvc框架提供了很多的View视图类型的支持，包括：jstlView、freemarkerView、pdfViewdg等。我们最常用的视图是jsp。

 - 说明

> 在springmvc的各个组件中，处理器映射器、处理器适配器、视图解析器称为springmvc的三大组件。可以认为是一个中心三个基本点，需要用户开发的组件有handler、view

 - 配置相关组件
	 - 在springmvc的配置文件中需要将新的处理器映射器和处理器适配器配置到容器中
	 
``` xml
<!-- 配置处理器映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHan
dlerMapping" />
<!-- 配置处理器适配器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHan
dlerAdapter" />
```


  [1]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502776986634.jpg