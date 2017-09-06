---
title: HQL查询 
tags: hibernate
grammar_cjkRuby: true
---

## 单表查询

>通过session调用`session.createQuery(hql)` 进行查询

 - 查询集合通过调用`list()`
 - 查询单个数据通过调用`uniqueResult`
 - 条件查询通过`setParameter(索引|别名占位符, 对应的数值)`
 - 排序查询`from User order by id desc`
 - 分页查询`setFirstResult(id数值) 和setMaxResults(总条数)`
 - 聚合函数和sql相同可以使用`sum(id),count(*),max(id),min(id),avg(id) 建议使用Number类型进行接收操作`

``` java
		String hql = "select count(id) from Teacher";
		String hql = "select count(id) from Teacher";
		Number num = (Number) session.createQuery(hql).uniqueResult();
		System.out.println(num);
```
 - 查询对象的某些属性，可以使用两种方法

``` java
		//返回的是对象数组
		String hql = "select id,name from Student";
		List<Object[]> list = session.createQuery(hql).list();
		for(Object[] arr : list){
			System.out.println("id:"+arr[0]+",name:"+arr[1]);
		}
```
``` java
		//需要在javabean中添加相应的构造方法（注意如果创建了构造
		//方法,那么必须再创建空参的构造方法）
		String hql = "select new Student(id,name) from Student";
		List<Student> list = session.createQuery(hql).list();
		System.out.println(list);
```
## sql多表查询回顾
**交叉连接(笛卡尔积)**

交叉连接为==select * from 表1 CROSS JOIN 表2== 可以简化 ==select  *  from 表1,表2==内连

**内连接**
1.隐式内连接`select * from 表1,表2 where 表1.字段 = 表2.字段`
2.显示内连接`select * from 表1 inner join 表2 on 表1.字段 = 表2.字段`

**外连接**
1.左连接`select * from 表1 left join 表2 on 表1.字段 = 表2.字段`
2.又连接`select * from 表1 right join 表2 on 表1.字段 = 表2.字段`

## HQL多表查询

**内连接**

1.内连接: `from Speaker s inner join s.videoSet` ,其结果是list集合,集合中放的是Object[] ,第一项是speaker 对象,第二项是video 对象

``` java
		String hql = "from Student s inner join s.teachers";
		List<Student> list = session.createQuery(hql).list();
		System.out.println(list);
```
2.迫切内连接: `select distinct s from Speaker s inner join fetch s.videoSet` ,其结果是list集合,集合中是Speaker 对象, hibernate 会自动帮助我们进行封装处理

**外连接**
1.左外连接`from Speaker s left join s.videoSet` 返回值类型是list,其中放的是Object[] ,第一项是speaker 对象,第二项是video 对象
2.迫切左外连接`select distinct s from Speaker s left join fetch s.videoSet` 其结果是list集合,集合中是Speaker 对象, hibernate 会自动帮助我们进行封装处理
3.右外连接(和左外连接相同,left换成right)
4.迫切右外连接(和迫切左外连接相同,left换成right)**注意需要做去重操作,添加select distinct s**

## QBC查询
通过session调用`session.createCriteria(类名.class)`
限定方法|说明
-|-
Restrictions.eq | equal,等于
Restrictions.allEq | 参数为Map对象,使用key/value进行多个等于的比对,相当于多个Restrictions.eq的效果
Restrictions.gt  | greatthan > 大于
Restrictions.ge | greatequal >= 大于等于
Restrictions.lt   | lessthan,< 小于
Restrictions.le  |lessequal <= 小于等于
Restrictions.between| 对应SQL的between子句
Restrictions.like | 对应SQL的LIKE子句
Restrictions.in | 对应SQL的in子句
Restrictions.and | and 关系
Restrictions.or | or 关系
Restrictions.isNull | 判断属性是否为空,为空则返回true
Restrictions.isNotNull | 与isNull相反
Restrictions.sqlRestriction | SQL限定的查询
Order.asc | 根据传入的字段进行升序排序
Order.desc | 根据传入的字段进行降序排序
MatchMode.EXACT | 字符串精确匹配.相当于”like ‘value’”
MatchMode.ANYWHERE | 字符串在中间匹配.相当于”like ‘%value%’”
MatchMode.START | 字符串在最前面的位置.相当于”like ‘value%’”
MatchMode.END | 字符串在最后面的位置.相当于”like ‘%value’”

- 查询集合通过调用`list()`
- 查询单个数据通过调用`uniqueResult()`
- 条件查询通过调用`add(Restrictions.eq())` 等操作
- 排序操作`addOrder(Order.asc("id"))`
- 统计操作`setProjection(Projections.rowCount());`

## 离线查询

> 在通常的web开发过程中,我们需要将web层提交的数据,传递给service,再由service传递给dao,最终在dao层通过factory创建session,由session创建查询,如果在dao层想要对所有的查询方法进行封装的话,因为不同的业务传递的参数会有所不同,所以很难封装,hibernate为我们提供了离线查询,也就是说可以在脱离session的情况下创建出来查询条件,由web层给service层,再由service层给dao,在dao层只需写一个方法就能完成就能完成很多工程的查询

- 创建离线`Criteria DetachedCriteria dc = DetachedCriteria.forClass(User.class);`
- 通过我们之前的QBC添加条件, `dc.add(Restrictions.idEq(1));`
- 当传递到dao层的时候可以通过dc调用方法,去绑定session, `Criteria criteria =
dc.getExecutableCriteria(session);`

