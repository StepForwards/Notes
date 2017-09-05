---
title: Hibernate多表关系 
tags: hibernate
grammar_cjkRuby: true
---
-
	* [一对多（多对一）](#一对多多对一)
		* [创建实体](#创建实体)
		* [配置ORM映射文件](#配置orm映射文件)
		* [创建测试文件](#创建测试文件)
		* [双方关系维护](#双方关系维护)
		* [级联操作](#级联操作)
		* [外键的维护权管理](#外键的维护权管理)
		* [双方关系维护、级联操作、外键维护权之间的关系](#双方关系维护-级联操作-外键维护权之间的关系)

## 一对多（多对一）

> 在一个视频管理的系统中应该包含这样两个表讲课人Speaker、视频Video。其中一个讲课人可以讲多个视频，而一个视频只能属于一个讲课人；在这里Speaker就是`一`的状态，Video就是`多`的状态。

### 创建实体

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
### 配置ORM映射文件

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


### 创建测试文件

> 需要==双方建立维护关系==，并且只有持久化状态才能写入数据库

``` java
public class One2ManyTest {
	
	private Session session;
	private Transaction transaction;
	
	//test开始之前执行
	@Before
	public void befor(){
		//使用工具类开启会话
		session = MyHibernateUtil.openSession();
		//开启事务
		transaction = session.beginTransaction();
	}
	//test结束之后执行
	@After
	public void after(){
		//提交事务
		transaction.commit();
		//关闭会话
		session.close();
		//关闭工厂
		MyHibernateUtil.closeFactory();
	}
	
	
	@Test
	public void test01(){
		
		Speaker speaker = new Speaker();
		speaker.setName("张三");
		
		Video video1 = new Video();
		Video video2 = new Video();
		video1.setTitle("video1");
		video2.setTitle("video2");
		
		//建立双方关系维护1，建立video与speaker的关系，video维护speaker对象（video属于speaker）
		video1.setSpeaker(speaker);
		video2.setSpeaker(speaker);
		//建立双方关系维护2，建立speaker与video的关系，speaker维护video对象（speaker拥有video1,video2）
		speaker.getVideoSet().add(video1);
		speaker.getVideoSet().add(video2);
		//通过save持久化speaker，video1，video2；因为数据只有处于持久化的状态才能被写入数据库
		session.save(speaker);
		session.save(video1);
		session.save(video2);
	}
}
```
![执行结果][1]


### 双方关系维护

> 在上述配置文件中我们已经通过外键建立了两个表的关系（联系），但是具体到相应的对象操作之间的关系是在我们动态的赋予的，可以认为在配置文件中我们建立了一个规则（或者说是通道），而实现这个规则的是具体的对象，我们知道在hibernate中==javabean==就是表，对象就是表中的记录，**双方关系维护就是对象通过规则去维护表与表中记录之间的关系（增，删，改，查）**。

> 需要注意的是双方关系维护一旦建立，在保存数据的时候必须保存（或者持久化）双方的全部对象，既在上述示例中如果我们仅仅保存了speaker（session.save(speaker)），而没有保存video1，video2；这样在程序执行的时候会报错，因为speaker维护video对象，在save（speaker）的时候，系统会找到speaker中的set集合中的video进而去添加video记录，但是由于video1，video2没有被持久化，因此无法修改vido记录speaker写入失败，导致数据库回滚。

### 级联操作

> 在上述示例中我们知道要想保存数据，我们必须将具有双方关系维护的对象全部转变到持久态，而这样将导致代码的繁琐性，级联操作因此而诞生，当我们将任何一个对象转变为持久态的时候，级联操作会将该对象的维护对象也转变为持久态，自然数据的存储不再受限，我们不再需要将所有的相关对象持久化，进而简化代码。

> 级联操作是在双方关系维护的基础的实现的，如果对象与对象之间没有建立双方关系维护，级联操作将没有任何意义。

级联操作==cascade==属性有3个值

 - ==save-update== 保存修改的时候级联操作
 - ==delete== 删除的时候级联操作，当删除一方的数据时，另一方也会被删除
 - ==all== 等同于 ==save-update,delete==

**设置speaker级联（save-update）**

``` xml
		<set name="videoSet" cascade="save-update" inverse="true">
			<key column="speakerId"></key>
			<one-to-many class="Video"/>
		</set>
```
此时我们仅仅执行`session.save(speaker);`即可将speaker，video1,video2保存到数据库。

设置video级联
`<many-to-one name="speaker" class="Speaker" cascade="save-update" column="speakerId"  ></many-to-one>`

此时我们仅仅执行`session.save(video1);`即可将speaker，video1,video2保存到数据库。

**对video进行级联操作（delete）**

> 当video设置了级联删除，那么在我们删除video对象的时候，会删除video对象所维护的speaker对象，由于外键的存在系统无法直接删除speaker对象，所以系统会在删除该speaker对象之前，设置所有维护该speaker的video的外键为==null==

### 外键的维护权管理

**冗余sql的产生**

在示例==test01()==中我们做了这样的操作

``` java
session.save(speaker);
session.save(video1);
session.save(video2);
```

控制台打印的sql
``` sql
Hibernate: insert into t_speaker (name) values (?)
Hibernate: insert into t_video (title, speakerId) values (?, ?)
Hibernate: insert into t_video (title, speakerId) values (?, ?)
Hibernate: update t_video set speakerId=? where id=?
Hibernate: update t_video set speakerId=? where id=?
```
奇怪的是为什么执行了5条sql语句？这就是外键维护权的问题。
我们知道video拥有对speaker的维护权，这里的维护权包含speaker中所有字段的维护权，既包含外键维护权；同样speaker也拥有对video的维护权。

> 在第一条sql执行后，speaker被保存到数据库（此时外键speakerId存在）
> 执行第二条sql的时候，video1被保存到数据库，因为speakerId存在，所以在插入video的时候speakerId也会被保存到video1对象中，也正因为如此在这里video没有对自身外键进行维护。
> 第三条sql同第二条sql。
> 第四条为speaker对video的外键维护（对于speaker来说其不知道speakerId已经存在），再次更新speakerId，但是speakerId已经存在并且没有发生新的更改，所以此次维护就显得多余了。
> 第五条同第四条。

*需要注意的是如果我们执行的顺序如下*
``` java
session.save(video1);
session.save(video2);
session.save(speaker);
```
*由于在插入video对象的时候speaker记录不存在，既speakerId不存在，video会自动维护外键，再加上speaker也会维护外键，因此将会执行7条sql语句（在不设置级联操作的情况下）*

**放弃外键的维护**

> 为了减少sql代码的冗余，提升性能，我们可以设置speaker放弃外键的维护权，由于video是外键的拥有者其对外键的维护权是无法放弃也不能放弃的。

设置speaker放弃外键维护权 `inverse="true"`

``` xml
		<set name="videoSet" inverse="true">
			<key column="speakerId"></key>
			<one-to-many class="Video"/>
		</set>
```
### 双方关系维护、级联操作、外键维护权之间的关系

> 双方关系维护是基于*.hbm.xml配置文件中的规则实现的，而规则是通过外键进行联系实现的，因此外键是双方关系维护权的核心。双方关系维护包含外键维护权，其操作的最终对象是数据库中的记录。
> 级联操作是基于双方关系维护实现的，其操作的对象是实体类，其目的是通过双方关系维护找到所有相关实体类并将其持久化，进而使实体类通过双方关系维护去修改数据库，其作用是辅助双方关系维护的实现。
> 外键维护权从属于双方关系维护，时双方关系维护的核心。需要注意的是如果speaker既一的一方放弃了外键维护权那么将无法通过持久化speaker对象级联更新（或插入）video，因为双方关系维护的核心是外键。

  [1]: https://www.github.com/StepForwards/my-notes/raw/images/Hibernate%E5%A4%9A%E8%A1%A8%E5%85%B3%E7%B3%BB/images/1504615907035.jpg