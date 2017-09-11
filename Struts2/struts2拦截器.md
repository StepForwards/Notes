---
title: struts2拦截器
tags: struts2
grammar_cjkRuby: true
---

拦截器是struts2实现功能的核心

**生命周期**

> 项目启动的时候创建拦截器，项目销毁的时候拦截器销毁

**创建方式**

拦截器的创建方式有3种

1. 实现Interceptor接口，实现3个方法，init方法，destory方法，intercept方法
2. 继承AbstractIntercept，实现抽象方法`intercept`，其本身也是实现`Interceptor`接口
3. 继承`MethodFilterInterceptor`实现抽象方法`doIntercept`，一般我们使用此方法进行创建

**拦截过程**

 - 拦截器的方法中有个参数`ActionInvocation invocation`，代表action对象，通过对象调用`invocation.invoke();`表示action方法的调用，也表示放行操作
 - 类似于spring中的环绕通知，写在它前面的代码叫做拦截器的前处理
 - 写在其后面的代码叫做其后处理程序
 - 对于返回值，一般用于不放行跳转到结果页面，如果是方向就将`invocation.invoke();`的返回值赋值给它

**配置拦截器**

``` java
public class AuthorityInterceptor extends MethodFilterInterceptor {
	//通过判断session中user是否为空，来判断用户是否登录，进而判断是否拦截
	@Override
	protected String doIntercept(ActionInvocation invocation) throws Exception {
		User user = (User) ActionContext.getContext().getSession().get("user");
		if(user == null){
			return "toLogin";
		}
		return invocation.invoke();
	}

}
```


``` xml
<!-- 配置拦截器 -->
	<package name="myInterceptor" namespace="/user" extends="struts-default">
		<interceptors>
			<!-- 注册拦截器 -->
			<interceptor name="authority" class="com.forward.ssh.web.interceptor.AuthorityInterceptor">
				<!-- 排除对指定方法的拦截 -->
				<param name="excludeMethods">login,execute</param>
			</interceptor>
			<!-- 自定义拦截栈 -->
			<interceptor-stack name="myInterceptorStack">
				<interceptor-ref name="authority"/>
				<interceptor-ref name="defaultStack"/>
			</interceptor-stack>
		</interceptors>
		<!-- 设置默认拦截栈 -->
		<default-interceptor-ref name="myInterceptorStack"/>
		<!-- 设置全局result -->
		<global-results>
			<result name="toLogin" >/WEB-INF/view/login.jsp</result>
		</global-results>
	</package>
```

 - 注册拦截器
 - 注册拦截器栈
 - 指定默认拦截器栈

**配置拦截器的排除**

> 默认情况下是会拦截package中所有的action

- 在对应的拦截器栈中的自定义的拦截器上添加`<param name="excludeMethods">add,delete</param>` 标签,指定排除规则
- 也可以指定拦截某些方法`<param name="includeMethods">add,delete</param>`

**配置全局异常界面**

``` xml
		<global-exception-mappings>
			<exception-mapping result="error" exception="java.lang.Exception"></exception-mapping>
		</global-exception-mappings>
```
**配置全局result**

``` xml
<global-results>
	<result name="error">/error.jsp</result>
</global-results>
```
