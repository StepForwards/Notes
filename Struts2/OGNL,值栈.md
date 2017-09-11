---
title: OGNL,值栈
tags: OGNL,struts2
grammar_cjkRuby: true
---

> 对象视图导航语言（Object-Graph Navigation Language）是一种表达式语言，struts2中整合了OGNL语言，其提供了强大功能，OGNL和EL表达式相似，EL是从11个域中获取数据，OGNL表达式是从OGNLContext中读取值。

## OGNL Context

OGNLContext由两个部分构成，ROOT和CONTEXT。OGNL表达式就是从这两个部分获取值

![OGNLContext][1]

ROOT部分可以放置任何对象，CONTEXT部分放置的是Map类型的键值对，且必须只能放键值对

### API
- 准备Root中的数据`User rootUser = new User("kk","pp");`
- 准备Context中的数据

``` java
Map<String, User> map = new HashMap<>();
map.put("user1", new User("张三", "123"));
map.put("user2", new User("李四", "234"));
```
- 创建`OGNLContext OgnlContext oc = new OgnlContext();`
- 向oc的root中放值`oc.setRoot(rootUser);`
- 向oc的context中放值`oc.setValues(map);`
- 获取root中的对象: `Object root = oc.getRoot();`
- 获取context中的对象: `Map context = oc.getValues();`
- 获取root中对象的name属性`String name = (String) Ognl.getValue("name", oc, oc.getRoot());`
- 获取context中user1的pwd属性属性`String pwd = (String) Ognl.getValue("#user1.pwd", oc, oc.getRoot());`
- 设置root中对象的name属性`Ognl.getValue("name='hello'", oc, oc.getRoot());`
- 设置context中user1的pwd属性并且获取`String pwd = (String)Ognl.getValue("#user1.pwd='pp',#user1.pwd",oc, oc.getRoot());`
- 调用root对象的get方法`String name = (String)Ognl.getValue("getName()", oc,oc.getRoot());`
- 调用context中user1的setName方法,并获取`String pp = (String) Ognl.getValue("#user1.setName('ioio'),#user1.getName()",oc, oc.getRoot());`
- 调用其他类的静态方法`Double t = (Double) Ognl.getValue("@java.lang.Math@random()", oc,oc.getRoot());`
- 获取类的静态成员变量`Double t1 = (Double) Ognl.getValue("@java.lang.Math@PI", oc,oc.getRoot());`
- 创建list对象,并获取长度`Integer size = (Integer)Ognl.getValue("{'tom','李雷','韩梅梅'}.size()", oc, oc.getRoot());`
- 取出数组中指定索引的内容`String content = (String) Ognl.getValue("{'tom','李雷','韩梅梅'}[0]",oc, oc.getRoot());` 或者写`String content = (String) Ognl.getValue("{'tom','李雷','韩梅梅'}.get(0)", oc, oc.getRoot());`
- 创建map,并获取长度`Integer mapSize = (Integer) Ognl.getValue("#{'name':'李雷','age':18}.size()", oc, oc.getRoot());`
- 获取map中指定key的值`String mapContent = (String) Ognl.getValue("#{'name':'李雷','age':18}['name']", oc, oc.getRoot());` 或者`String mapContent = (String)Ognl.getValue("#{'name':'李雷','age':18}.get('name')", oc, oc.getRoot());`

## 值栈（ValueStack）

> struts2为我们提供了OGNLcontext，并且在root部分和Context部分放置了很多数据，我们需要使用OGNL表达式获取对应的OGNLContext中的数据，struts2把这个OGNLContext对象起名字为ValueStack，也就是我们所说的struts中的值栈本质就是一个OGNLContext对象

 - 值栈中的root部分就是一个集合，是栈的结构，其本质就是一个`ArrayList`
 - 值栈中的context部分就是我们之前介绍的ActionContext（其本质就是一个Map）
 - 获取值栈对象，可以通过`ValueStack valueStack = ActionContext.getContext().getValueStack();`

### 栈结构

 - 栈是一种存储结构
 - 栈的特点是**先入后出，后入先出**
 - 对于栈的操作一般都是有两个方法`push()`和`pop()`
 - 我们可以通过一个ArrayList来实现栈的结构,当调用push方法的时候,将索引为0的位置放入数据,调用pop方法的时候将索引为0的内容删除并返回就实现了压栈和弹栈的过程
- 通过查看源代码可以发现值栈中的root部分就是按照上述内容进行实现的
- 对于ValueStack,因为root部分是一个栈的结果,当我们通过OGNL表达式调用某个属性的时候,他会从栈顶依次向栈低查找对应的数据,如果发现了就停止

### 通过debug标签查看值栈

 - 在转发后的jsp页面导入标签库struts的标签库`<%@  taglib prefix="s" uri="/struts-tags" %>`
 - 在界面上书写`<s:debug></s:debug>`标签，会生成debug标签
 - 栈中默认放置的就是当前访问的action对象

### 值栈的生命周期

> 通过查看源码我们可以发现，值栈的创建在于一次请求，每次请求都会创建一个ActionContext对象，并且创建一个值栈；值栈的生命周期和ActionContext和Request生命周期相同，存活于同一个请求

 [1]: https://www.github.com/StepForwards/my-notes/raw/images/OGNL%E8%A1%A8%E8%BE%BE%E5%BC%8F%E8%AF%AD%E6%B3%95/images/1505128766622.jpg