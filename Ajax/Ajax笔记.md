---
title: Ajax笔记 
tags: ajax,jquery
grammar_cjkRuby: true
---
## 原生AJAX
### 创建XMLHttpRequest对象

``` javascript
var xhr = new XMLHttpRequest();
```
老版本IE5和IE6使用ActiveX对象

``` javascript
var axo = new ActiveXObject("Microsoft.XMLHTTP");
```
### 向服务器发送请求

``` javascript
xhr.open("GET","/test.action",true);
xhr.send();
```

| 方法                   | 描述 |
| ---------------------- | ---- |
| open(method,url,async) | 规定请求的类型、URL以及是否异步处理 |
| send(string)           | 将请求发送到服务器 |

- method：请求的类型；GET或POST
- url：文件在服务器上的位置
- async：true（异步）或false（同步）
- string：仅限于POST请求
	- post请求无法缓存文件，
	- 数据量无限制
	- 安全

### 服务器响应
|属性|描述|
|-|-|
|responseText|获得字符串的响应数据|
|responseXML|获取XML形式的响应数据|

### onreadystatechange事件
每当readyState改变时，就会触发onreadystatechange事件
|属性|描述|
|-|-|
|onreadystatechange|存储函数（或函数名），每当readyState属性改变时，就会调用该函数。|
|readyState|存有XMLHttpRequest的状态。从0到4发生变化|
|status|200："ok" 404：未找到页面|

 - 0：请求未初始化
 - 1：服务器连接已建立
 - 2：请求已接收
 - 3：请求处理中
 - 4：请求已完成，且响应已就绪

## JQueryAJAX

### $.get&$.post

``` javascript
$.get(url,[data],[callback],[type])
$.post(url,[data],[callback],[type])
```
 - url：待载入页面的URL地址
 - data：待发送Key/value参数
 - callback：载入成功时回调函数
 - type：返回内容格式，xml，html，script，json，text，_default

``` javascript
				$.get(
    				"${pageContext.request.contextPath}/user/loginCheckname.action",
    				"name="+this.value,
    				function(message){
    					if(message.success === false){
    						$("span").text("用户不存在");
    					}else{
    						alert(message.success);
    						$("span").text("");
    					}
    				},
    				"json"
				);
```
post或者get发送的数据只能是以下两种类型不能是json字符串：

 - 序列化格式：k=v&k=v&k=v
 - json对象：{"k":v,"k":v}

在Controller层接收数据的时候只需要使用model类进行接收
返回内容格式为json格式时需要在Controller方法中返回值model类中添加@ResponseBody注解，解析model类为json对象

``` java
@RequestMapping("/loginCheckname.action")
	@ResponseBody
	public Message login(User u){
		Message message = new Message();
		message.setSuccess(us.checkName(u) == null ? false : true);
		return message;
	}
```
### $.ajax

该方式支持get提交以及post提交并且可以提交json字符串
提交数据为json字符串的时必须在Controller层相应方法中的参数添加@RequestBody注解

``` javascript
$.ajax({
    				data:'{"name":'+this.value+'}',
    				dataType:"json",
    				contentType:"application/json;chartset=utf-8",
    				success:function(message){
    					if(message.success === false){
    						$("span").text("用户不存在");
    					}else{
    						$("span").text("");
    					}
    				},
    				type:"post",
    				url:"${pageContext.request.contextPath}/user/loginCheckname.action"
    			});
```



