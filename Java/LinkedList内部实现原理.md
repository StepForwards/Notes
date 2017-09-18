---
title: LinkedList内部实现原理
tags: java,list
grammar_cjkRuby: true
---
同**ArrayList内部原理**一样

我们先创建一个LinkedList对象`LinkedList<String> li = new LinkedList<>();`，然后查看其构造方法

``` java
	transient Node<E> first;
	
	transient Node<E> last;
	
	public LinkedList() {
    }
```
比较尴尬的是在它的构造方法中什么也没有写。`LinkedList`的结构是**==双向链表结构==**，链表结构数据是存储在节点中的，我们正好看到两个`Node`节点属性，一个叫`first`代表链表中第一个节点，另一个叫`last`代表链表中最后一个节点。

查看一下`Node`类
``` java
 	private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
```
这是一个**内部类**，标准的双向链表的数据结构`Node<E> prev`**前驱**指向该节点的前一个节点，`Node<E> next;`**后继**指向该节点的后一个节点，` E item;`**元素**用于存放数据。

当我们执行`add()`方法向`LinkedList`中添加数据的时候，调用以下方法

``` java
	public boolean add(E e) {
        linkLast(e);
        return true;
    }

	void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
```
我们发现它将`last`（最后一个节点）给了`l`节点，然后`new`了一个新的节点初始化新节点的前驱为`l`节点，后继为`null`,元素为我们要存放的对象。然后设置`newNode`为`last`节点，如果节点`l`为`null`则设置`newNode`为`first`节点，否则使`l`的`next`指向新的节点 `l.next = newNode`。

![FirstLastNode][1]

![内部原理][2]


  [1]: https://www.github.com/StepForwards/my-notes/raw/images/LinkedList%E5%86%85%E9%83%A8%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86/images/1505738934451.jpg
  [2]: https://www.github.com/StepForwards/my-notes/raw/images/LinkedList%E5%86%85%E9%83%A8%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86/images/1505739022559.jpg