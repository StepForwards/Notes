---
title: Hibernate多表关系 
tags: hibernate
grammar_cjkRuby: true
---
## 一对多（多对一）

> 在一个视频管理的系统中应该包含这样两个表讲课人Speaker、视频Video。其中一个讲课人可以讲多个视频，而一个视频只能属于一个讲课人；在这里Speaker就是`一`的状态，Video就是`多`的状态。

**创建实体**

 - 在Speaker中添加==set==集合（如果添加==list==集合需要在配置文件中额外配置标签==index==）
 
``` java
public class Speaker {
	
	private Integer id;
	private String name;
	private Set<Video> videoSet = new HashSet<>();
	
	...//以下的set,get方法省略
}
```
 - 在Video中添加==类类型==Speaker
 

``` java
public class Video {
	
	private Integer id;
	private String title;
	private Integer speakerId;
	private Speaker speaker;
	
	...//省略set,get方法
}
```
**配置ORM映射文件**
