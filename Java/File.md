---
title: File
tags: java
grammar_cjkRuby: true
---

## File类
**路径问题**

 - 在windows中的路径的分隔符`\`
	 - 在使用java代码写的时候，因为`\`就是转义字符，所以应该写成`\\`也可以写成`/`
 - 在linux中的路径的分隔符是`/`

**构造方法**

``` java
		//通过将给定的路径名字符串转换为抽象路径名来创建新的File实例
		File file01 = new File("d:/a/b/1.txt");
		//从父路径名字符串和子路经名字符串创建新的File实例		
		File file02 = new File("d:/a/b","1.txt");
		File parent = new File("d:/a/b");
		//从父抽象路径名和子路经名字符串创建新的File实例
		File file03 = new File(parent, "1.txt");
```
**创建删除目录和文件**

``` java
		File parent = new File("d:/a/b");
		File file = new File(parent, "1.txt");
		System.out.println(file.createNewFile());
```
 - 创建文件功能
	- 创建过程中如果没有中间目录，系统会报错
	- 如果文件已经存在了，哪么调用方法后的返回值为false
	- 如果不指定后缀名默认还是创建文件，不会创建文件夹
	- 对于路径也可以写为`d:/a/b/123.txt`


``` java
		File parent = new File("d:/a/b");
		System.out.println(parent.mkdir());
```
 - 创建文件夹功能
	 - 只能创建1级文件夹，多级目录会报错
	 - 如果要创建多层目录需要使用`file.mkdirs（）`

``` java
		File parent = new File("d:/a/b");
		File file = new File(parent, "1.txt");
		file.delete();
		parent.delete();
```
 - 删除文件或者文件夹
	 - 如果有中间目录删除会失败
	 - 删除不会放进回收站

**File的获取功能**

 - `file.getName()`通过路径的`split("/")`数组的最后一个元素
 - `file.getPath()`得到创建file的路径，创建file时的路径是什么得到的就是什么
 - `file.getAbsolutePath()`获取绝对路径，如果是file中的路径是相对路径，得到的是相对于当前项目的绝对路径
 - `file.getAbsoluteFile()`返回的是文件
 - `file.length()`获取的是文件的字节数，long类型
 - `file.geParent()`得到的是父目录，返回值是String类型
 - `file.getParentFile()`得到的是父目录，返回值是file类型

**File判断功能**

 - 判断路径是否存在`boolean exists = file.exists();`
 - 判断是不是文件夹`boolean exists = file.isDirectory();`
 - 判断是否是文件`boolean exists file.isFile();`

**遍历目录**

 - 遍历目录`File file = new File("D:/Develop/eclipse");
	File[] listFiles = file.listFiles`返回值是File数组
	 - 如果使用`String[] list = file.list();`返回值是字符串数组
 - 获取磁盘根目录
``` java
		File file = new File("D:/Develop/eclipse");
		File[] listRoots = File.listRoots();
		for(File f : listRoots){
			System.out.println(f);
		}
```
**文件过滤器**

> 通过设置可以获得指定类型的文件

 - 通过调用`file.listFiles(new FileFilter(){});`创建一个FileFilter的文件过滤器
 - 重写抽象方法，当方法返回值是true的时候，添加进数组，实现文件过滤的功能

``` java
		File file = new File("D:/Develop/eclipse");
		File[] listFiles = file.listFiles(new FileFilter() {
			
			@Override
			public boolean accept(File pathname) {
				return pathname.getName().toLowerCase().endsWith(".jar");
			}
		});
```
**文件过滤原理**

 - `file.listFiles()`每次获取到一个文件或者文件夹的时候，就会调用对应接口的`accept`方法，将得到的这个文件或者文件夹作为参数进行传递
 - `accept()`方法如果返回值是true，就将此文件或者文件夹放入对应的数组中，所以我们可以用我们的代码进行控制返回值是`true`还是`false`

	
		