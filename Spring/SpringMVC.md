---
title: SpringMVC
tags: spring,springMVC
grammar_cjkRuby: true
---
[toc]

	* [SpringMVC](#springmvc)
		* [SpringMVC处理流程](#springmvc处理流程)
		* [Hello World](#hello-world)
		* [SpringMVC组件介绍及其配置](#springmvc组件介绍及其配置)
		* [注解驱动](#注解驱动)
		* [视图解析器](#视图解析器)
		* [静态资源放行](#静态资源放行)
			* [方式一](#方式一)
			* [方式二](#方式二)
		* [参数绑定](#参数绑定)
			* [默认支持参数类型](#默认支持参数类型)
			* [简单类型参数](#简单类型参数)
			* [@RequestParam](#requestparam)
			* [参数绑定model类](#参数绑定model类)
			* [参数邦定包装类](#参数邦定包装类)
			* [数组参数的绑定](#数组参数的绑定)
			* [集合类型参数绑定](#集合类型参数绑定)
		* [解决乱码](#解决乱码)
		* [自定义格式类型转换](#自定义格式类型转换)
			* [日期格式化](#日期格式化)
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

==springmvc.xml==
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

==springmvc.xml==
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

==controller==
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

==springmvc.xml==
``` xml
<!-- 配置处理器映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHan
dlerMapping" />
<!-- 配置处理器适配器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHan
dlerAdapter" />
```
![处理器][2]
![过时的处理器映射器][3]![过时的处理器适配器][4]

### 注解驱动
直接配置处理器映射器和处理器适配器比较麻烦，可以使用注解驱动来加载。
SpringMVC使用`<mvc:annotation-driven/>`自动加载RequestMappingHandlerMapping和RequestMappingHandlerAdapter可以在springmvc.xml配置文件中使用`<mvc:annotation-driven/>`代替注解处理器和适配器的配置。

![注解的方式配置处理器映射器及适配器][5]

### 视图解析器

> 通过配置视图解析器的前缀和后缀在前端控制器将ModelAndView交过视图解析器的时候，自动给视图添加前缀和后缀

==springmvc.xml==
``` xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix" value="/WEB-INF/view/"></property>
	<property name="suffix" value=".jsp"></property>
</bean>
```
==controller==
``` java
package com.forward.ssm.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import com.forward.ssm.model.Role;
import com.forward.ssm.service.RoleService;

@Controller
public class RoleController {
	
	@Autowired
	RoleService rs;
	
	@RequestMapping("/role/roleList.action")
	public ModelAndView roleList(){
		ModelAndView mv = new ModelAndView();
		List<Role> list = rs.findAllRole();
		mv.addObject("list", list);
		mv.setViewName("/role/roleList");
		return mv;
	}
	
}

```
### 静态资源放行

> 前端控制器的本质是一个servlet，所以==url-pattern==符合servlet的规则，但是springmvc是需要拦截请求才能使用框架进行处理的，所以关于路径匹配上一般不会使用完全路径匹配和目录匹配，通常设置为后缀名进行匹配（推荐使用这种方式），也可以使用==/==和==/*==，但是这样做会拦截静态资源，需要我们在Controller中进行静态资源的操作。
> **注意：如果前端控制器配置为扩展名匹配的话，是不需要设置静态资源放行的，所以建议使用扩展名匹配**

#### 方式一

> tomcat内部有一个叫做默认servlet的==url-pattern==为==/==其可以处理静态资源，我们可以在配置文件中配置一个默认的servlet为这个，当所有路径都不匹配时让这个servlet进行处理，必须要配置注解驱动才能正常使用

==springmvc.xml==
``` xml
<mvc:default-servlet-handler/>
```
#### 方式二

> 手动建立映射关系，==/**== 表示可以有下一级文件夹，这种映射路径的方法，内部是由处理器映射器去执行具体的映射过程，因此必须要配置注解驱动如果不配置注解驱动会导致web应用无法访问，此方法的好处是路径可以直接在配置文件中进行指定

==springmvc.xml==
``` xml
<mvc:resources location="/image/" mapping="/image/**"></mvc:resources>
<mvc:resources location="/css/" mapping="/css/**"></mvc:resources>
<mvc:resources location="/js/" mapping="/js/**"></mvc:resources>
<mvc:resources location="/lib/" mapping="/lib/**"></mvc:resources>
```
### 参数绑定

#### 默认支持参数类型

> 如果我们定义了请求，响应，**session**，**springmvc**就会将**HttpServletRequest**，**HttpServletResponse**，**HttpSession**三个对象作为参数传递过来，我们在定义参数的时候还可以写**Model/ModelMap**用来向页面传值。
==controller==
``` java
@RequestMapping("/role/roleList.action")
	public ModelAndView roleList
	(HttpServletRequest req,HttpServletResponse res,HttpSession session,Model md,ModelMap mm){
		System.out.println(req+","+res+","+session+","+md+","+mm);
		return null;
	}
```
#### 简单类型参数

> 参数类型推荐是使用包装数据类型，因为基础数据类型不可以为null

 - **整型：Integer、int**
 - **字符串：String**
 - **单精度：Float、float**
 - **双精度：Double、double**
 - **布尔型：Boolean、boolean**

> 说明：对于布尔类型的参数，请求的参数值为true或false或1或0

#### @RequestParam

> 如果表单中的name属性和参数名称不一致可以使用==@RequestParam==建立映射关系，一旦使用了这个注解，传递的参数就必须含有该名称如果没有就会报错误

![注解中的参数错误][6]

 - required属性表示页面传过来的值是否是必须的默认是true
 - defaultValue表示当其为空时的默认值

#### 参数绑定model类

> 须保证表单的name属性和model中的属性彼此对应，需要设置set方法。

![表单中name设置][7]
==controller==
``` java
	@RequestMapping("/role/editRole.action")
	public ModelAndView editRole(Role r){
		System.out.println(r);
		rs.updateRoleById(r);
		return null;
	}
```
#### 参数邦定包装类

> 如果是包装类中的属性，需要将表单信息中的name属性名设置为类中属性的属性

==RoleVO==
``` java
public class RoleVO {
	
	private Role r;
	
	public Role getR() {
		return r;
	}
	public void setR(Role r) {
		this.r = r;
	}
```
```java
	@RequestMapping("/role/editRole.action")
	public ModelAndView editRole(RoleVO rv){
		rs.updateRoleById(rv);
		return null;
	}
```
![jsp页面包装类,name属性设置][8]

#### 数组参数的绑定

> 在表单中如果有checkbox这种多选框，提交到controller可以通过数组的形式获取，可以将checkbox的name属性设置为方法的参数名称，也可以在包装类中定义成员变量的名字为表单中的name属性值

![在包装类中定义][9]

![jsp,controller设置][10]


#### 集合类型参数绑定

> 在表单中如果牵扯到表格中的数据批量修改，并且每一行有多个数据都要更改且有多行，我们可以把每一行当作一个对象，把多行数据放入对象集合中进行提交，**集合类型参数不能直接使用，必须将其作为包装类的属性使用**

``` html
 				<td><input type="text" name="list[${status.index }].rName" value="${ role.rName }"></td>
                <td><input type="text" name="list[${status.index }].rDesc" value="${ role.rDesc }"></td>
                <td><input type="hidden" name="list[${status.index }].rId" value="${role.rId}"></td>
```
![在包装类中添加list集合属性][11]
==controller:==
```java
	/*
	 * 这种情况仅仅适合包装类，不能将集合直接赋值给参数
	 */
	@RequestMapping("/role/batchRole.action")
	public ModelAndView batchRole(RoleVO rv){
		rs.updateBatchRole(rv);
		return null;
	}
```

### 解决乱码

> 在==web.xml==中配置编码过滤器

``` xml
  <filter>
  	<filter-name>CharacterEncodingFilter</filter-name>
  	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  	<init-param>
  		<param-name>encoding</param-name>
  		<param-value>UTF-8</param-value>
  	</init-param>
  </filter>
  <filter-mapping>
  	<filter-name>CharacterEncodingFilter</filter-name>
  	<url-pattern>*.action</url-pattern>
  </filter-mapping>
```
### 自定义格式类型转换

> 可以进行日期的转换，以及数据的相应格式化（比如将表单提交的数据加前后缀）springmvc给我们提供一个转换器工厂，我们可以通过这个转换器工厂生产很多转换类（需要实现Convert接口）按照我们的要求进行数据的格式转换，对方法参数赋值的事情是处理器适配器做的，所以配置工作应该交过处理器适配器，而在springmvc中我们已经通过注解配置了这个组件，因此配置应该在==<mvc:annotation-driven/>==进行设置。

 - 在springmvc的配置文件中，配置一个转换器工厂的bean

==Springmvc.xml:==
``` xml
<!-- 使用注解的方式加载处理器映射器和处理器适配器，添加自定义转换服务 -->
<mvc:annotation-driven conversion-service="converionService"/>
<!-- 配置转换器工厂，并将转换器交给工厂处理 -->
<bean name="converionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
	<property name="converters">
		<set>
			<bean class="com.forward.ssm.util.DateConvert"></bean>
		</set>
	</property>
</bean>
```
==util：==
``` java
public class DateConvert implements Converter<String, Date>{
	//Converter<S, T>
	//S:source,需要转换的源的类型
	//T:target,需要转换的目标类型
	@Override
	public Date convert(String str) {
		DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm");
		Date date = null;
		try {
			date = df.parse(str);
		} catch (ParseException e) {
			e.printStackTrace();
		}
		return date;
	}
}
```
#### 日期格式化

> 对于日期格式，我们可以直接通过注解的形式对POJO的成员变量进行日期的格式化

![包装类中使用注解进行日期格式化][12]

==jsp中：==
``` html
<td>修改时间</td>
<td colspan="3" class="control">
<input name="r.rUpdatetime" type="datetime-local" value="<fmt:formatDate value="${role.rUpdatetime }" type="both" pattern="yyyy-MM-dd'T'HH:mm" />" >
</td>
```
==controller：==
```java
	@RequestMapping("/role/editRole.action")
	public ModelAndView editRole(RoleVO rv){
		System.out.println(rv);
		rs.updateRoleById(rv);
		return null;
	}
```

  [1]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502776986634.jpg
  [2]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502794541934.jpg
  [3]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502794692913.jpg
  [4]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502794736500.jpg
  [5]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502795310021.jpg
  [6]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502798033282.jpg
  [7]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502798478213.jpg
  [8]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502798990383.jpg
  [9]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502799446674.jpg
  [10]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502799566091.jpg
  [11]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502800421267.jpg
  [12]: https://www.github.com/StepForwards/my-notes/raw/images/SpringMVC/images/1502802577923.jpg