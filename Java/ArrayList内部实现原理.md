---
title: ArrayList内部实现原理
tags: java,list
grammar_cjkRuby: true
---

首先，我们`new`一个对象list集合
``` java
	List<String> list = new ArrayList<>();
```
我们知道对象的创建离不开构造方法，因此我们查看ArrayList源码的时候先看其构造方法

``` java
	private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
	transient Object[] elementData; // non-private to simplify nested class access
	
	public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```
通过源码我们发现在`new ArrayList<>()`的时候，其实质是获得一个`Object[]`数组；
**==因此ArrayList的本质就是一个数组==**
然后我们向元素中添加一个对象通过`add()`方法，并查看add的源码

``` java
	list.add("123");
```
``` java
	private int size;

 	public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
```
在add方法中调用了一个方法`ensureCapacityInternal（）`用来初始化容量
然后向数组的末尾添加了一个元素

接下来我们来看看这个`ensureCapacityInternal（）`方法的内部是如何实现的

``` java
	private static final int DEFAULT_CAPACITY = 10;

	private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }
```
很显然在ArrayList被创建的时候执行了`this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;`，因此`if`语句是成立的，我们知道当我们第一次执行`add("123")`的时候`size=0`，因此该方法的参数是1。所以`minCapacity=10`
然后我们看` ensureExplicitCapacity(minCapacity);`这个方法的内部。

``` java
	protected transient int modCount = 0;

	private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```
因为`elementData`是一个`null`的对象数组，所以`elementData.length=0`,`if`条件成立执行`grow(minCapacity)`

``` java
	 private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

	private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
因为`minCapacity=0`，所以`newCapacity=0`，导致`if(newCapacity - minCapacity<0)`成立，结果` newCapacity = minCapacity;`等于10；
`MAX_ARRAY_SIZE= Integer.MAX_VALUE - 8;`可以认为`MAX_ARRAY_SIZE`是一个无穷大的数，因此` if (newCapacity - MAX_ARRAY_SIZE > 0)`语句不成立，`elementData = Arrays.copyOf(elementData, newCapacity);`拷贝原数组到新数组，新数组长度为10；

当我们继续添加直到`ArrayList`的`size=10`，我们继续`add`第11个对象的时候`if (minCapacity - elementData.length > 0)`成立，

``` java
 		int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
```
因此，**==当ArrayList容量存满的时候，会扩容为原来的3/2倍==**

> 通过内部原理可知,对于ArrayList的每次操作,当需要扩容的时候,每次扩容为当前长度的1.5倍,,但是每次操作集合都需要进行数组的拷贝,此过程消耗资源,所以如果对于经常对集合进行添加删除操作的时候,不建议使用ArrayList

![ArrayList内部原理][1]


  [1]: https://www.github.com/StepForwards/my-notes/raw/images/ArrayList%E5%86%85%E9%83%A8%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86/images/1505737052730.jpg