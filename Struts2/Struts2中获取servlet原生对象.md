---
title: Struts2中获取servlet原生对象
tags: servlet,struts2
grammar_cjkRuby: true
---
## 获取servlet原生对象

> 在struts2中,给我们提供了一个ActionContext类,在这个类中存放了所有我们学习servlet时的对象,这个类的本质就是一个map集合

**存放的内容**
- request对象
- response对象
- servletContext对象
- request域,把request中存放数据的区域单独分离出来本质就是一个map
- session域,把session中存放数据的区域单独分离出来本质就是一个map
- application域,把application中存放数据的区域单独分离出来本质就是一个map
- param参数,把之前我们操作的request.getparamterMap中的map分离出来
- attr域,把request域,session域,application域合在一起
- valueStack 值栈

**生命周期**

> 每次请求的时候创建ActionContext对象,请求结束的时候销毁

**内部原理**

> 当请求发生的时候,struts2就会创建ActionContext对象,并将这个对象和当前本地线程进行绑定,所以就算有100个用户同时访问,那么在系统内存中就有100个ActionContext对象,但是它是和线程进行绑定的,所以对于一个请求而言,在在请求没有结束的时候获取的都是当前请求对应的那个ActionContext

**获取方式**

通过ActionContext方式获取

- 获取ActionContext对象ActionContext context = ActionContext.getContext();
- 获取session域Map<String, Object> session = context.getSession();
- 获取application域Map<String, Object> application = context.getApplication();
- 获取request域Map<String, Object> req = (Map<String,Object>)context.get("request");
- 向ActionContext中放置数据context.put(key, value);
- 从ActionContext中获取数据context.get(key)
- 对于request而言,ActionContext不建议获取request对象,因为ActionContext对象的生命周期和request的生命周期一致,并且在struts2的核心过滤器中,可以看到,struts2对原生的request对象进行了代理操作,在后续的request对象操作的过程中,其实是操作的代理对象,其代理对象已经将原生req的getAttribute方法修改,会既找原生request域,又会找ActionContex中的内容

![代理增强][1]

通过ServletActionContext方式获取

> 它是一个工具类,通过这个工具类可以获取到原生的request,response,session,servletContext,但是通过查看源码发现其内部还是从ActionContext中获取的

- 获取原生request对象`ServletActionContext.getRequest()`
- 获取原生response对象`ServletActionContext.getResponse()`
- 获取原生servletContext对象`ServletActionContext.getServletContext()`
- 获取原生session对象是通过获取的request对象进行获取的


*通过实现接口的方式获取*

> 我们可以通过实现ServletRequestAware, ServletContextAware,SessionAware,ServletResponseAware这四个接口来获取对应的4个原生对象,我们可以定义4个成员变量,在这个方法中将参数的值赋值给我们的成员变量,这些方法会在execute方法调用之前进行调用,所以在execute方法中就可以使用这些原生servlet对象

![通过接口获取][2]


  [1]: https://www.github.com/StepForwards/my-notes/raw/images/Struts2%E4%B8%AD%E8%8E%B7%E5%8F%96servlet%E5%8E%9F%E7%94%9F%E5%AF%B9%E8%B1%A1/images/1504791967191.jpg
  [2]: https://www.github.com/StepForwards/my-notes/raw/images/Struts2%E4%B8%AD%E8%8E%B7%E5%8F%96servlet%E5%8E%9F%E7%94%9F%E5%AF%B9%E8%B1%A1/images/1504792609661.jpg