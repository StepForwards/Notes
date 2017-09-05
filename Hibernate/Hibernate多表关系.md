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

> 在数据库中两张表的关系是通过==外键==联系并维护的，因此我们需要指定video的speakerId为外键映射Speaker的主键以建立两个表的关系。

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
    
<hibernate-mapping package="com.zhiyou100.hibernate.model">
	<class name="Speaker" table="t_speaker" >
		<!-- 设置主键及主键生成策略 -->
		<id name="id" >
			<generator class="native"></generator>
		</id>
		<property name="name"></property>
		<set name="videoSet" >
			<!-- 设置外键并指定set集合泛型 -->
			<key column="speakerId"></key>
			<one-to-many class="Video"/>
		</set>
		<!-- list集合配置
			<list name="videoSet">
				<key column="speakerId"></key>
				<index column="colIndex" type="java.lang.Integer"></index>
				<one-to-many class="Video"/>
			</list>
		-->
	</class>
</hibernate-mapping>
```
``` xml
<hibernate-mapping package="com.zhiyou100.hibernate.model">
	<class name="Video" table="t_video" >
		<id name="id">
			<generator class="native"></generator>
		</id>
		<property name="title"></property>
		<!-- 配置类类型的name，全类名，以及Video表的外键（用video表的外键联系两个表） -->
		<many-to-one name="speaker" class="Speaker" column="speakerId"  ></many-to-one>
	</class>
</hibernate-mapping>
```
**创建测试文件**

> 需要双方建立维护关系，并且只有持久化状态才能写入数据库

``` java
public class One2ManyTest {
	
	private Session session;
	private Transaction transaction;
	
	@Before
	public void befor(){
		session = MyHibernateUtil.openSession();
		transaction = session.beginTransaction();
	}
	
	@After
	public void after(){
		transaction.commit();
		session.close();
		MyHibernateUtil.closeFactory();
	}
	
	
	@Test
	public void test01(){
		Speaker speaker = new Speaker();
		speaker.setName("八王");
		
		Video video1 = new Video();
		Video video2 = new Video();
		video1.setTitle("video1");
		video2.setTitle("video2");
		video1.setSpeaker(speaker);
		video2.setSpeaker(speaker);
		
		speaker.getVideoSet().add(video1);
		speaker.getVideoSet().add(video2);
		session.save(speaker);
		session.save(video1);
		session.save(video2);
	}
}
```
