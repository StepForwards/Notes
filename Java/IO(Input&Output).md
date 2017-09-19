---
title: IO(Input&Output)
tags: io
grammar_cjkRuby: true
---

> Input和Output是相对于内存来说的，当我们读取计算机中的文件的时候，硬盘中的文件被读取的到内存中即为Input(读取进内存)，当内存中的文件被序列化既被保存到硬盘的时候即为Output(从内存中输出到硬盘)

## OutputStream

> OutputStream叫做字节输出流，每次操作的是1个字节（8位），因为计算机中最小的存储单位是字节，所以OutputStream可以写入任何文件
> OutputStream此抽象类，是表示输出字节流的所有类的超类。操作的数据都是字节，定义了输出字节流的基本共性功能方法。

 - `write(int b)`写入一个字节
 - `write(byte[] b)`写入字节数组
 - `write(byte[] b,int off,int len)`写入字节数组b,off表示从数组中哪个索引开始写，写多少个长度
 - `close()`关闭对象释放相应资源，有时如果不写close就会造成文件一直被占用无法删除

**FileOutputStream**

> FileOutputStream类，即为文件输出流，是用于将uujuxxruFile的输出流。

**写入流程**

 - 创建FileOutputStream指定输出地址`FileOutputStream fos = new FileOutputStream("d:/a.txt");`如果在`d:/`不存在`a.txt`文件，将会自动创建
 - 如果路径中含有中间目录并且中间目录是不存在的（地址不存在）是无法写入成功的
 - `fos.write(100)；`将100转换为2进制写入文件，会参照对应的ASCLL码表
 - 关闭输出流`fos.close()`

**写入方式**

 - 写入一个字节`fos.write(100);`
 - 写入一个字节数组`byte[] bytes = {65,66,67};fos.write(bytes);`
 - 写入字节数组中的一部分`byte[] bytes = {65，66，67}；fos.write(bytes,0,2);`
 - 如果要写入一个字符串，可以通过字符串调用`"大家好".getBytes()`方式将字符串转换为一个字节数组的方式
 - 如果需要多次写入并且进行换行操作的话，需要添加`\r\n`
 - 如果需要每次写入内容是在当前文件的末尾添加，而不是进行覆盖，就需要创建FileOutputStream的时候使用`FileOutputStream fos = FileOutputStream("d:/a.txt",true);`

## InputStream

 - `int read（）`读取一个字节并返回，没有字节返回-1
 - `int read（byte[]）`读取一定量的字节数，并存储到字节数组中，返回读取到的字节数。
 - `void close（）`关闭输入流

**FileInputStream**

> FileInputSstream类,即为文件输入流，是用于将数据读取到程序的输入流。

**构造方法**

 - `FileInputStream（File file）`通过打开与实际文件的连接创建一个FileInputStream，该文件由文件系统中的File对象 file命名。
 - `FileInputStream（String name）`通过打开与实际文件的连接创建一个FileInputStream，该文件由文件系统中的路径名 name命名。

**读取方式**

 - 读取单个字节
	 - read的返回值代表读取的内容，文件末尾的值为-1
``` java
		FileInputStream fileIntputStream = new FileInputStream("d:/1.txt");
		int len = 0;
		while( (len = fileIntputStream.read()) != -1)
			System.out.print((char)len);
		fileIntputStream.close();
```
 - 读取字节数组
	 - read的返回值代表读取到了多少个有效字节读取的内容放在byte中

``` java
		FileInputStream fileIntputStream = new FileInputStream("d:/1.txt");
		byte[] b = new byte[2];
		//返回值代表读了多少个有效字节，内容放在byte数组中。
		int len;
		while((len = fileIntputStream.read(b)) != -1){
			System.out.print(new String(b,0,len));
		}
		fileIntputStream.close();
```
*文件的拷贝*

``` java
	//文件的拷贝
	@Test
	public void test04() throws IOException{
		FileInputStream fileIntputStream = new FileInputStream("d:/1.txt");
		FileOutputStream fileOutputStream = new FileOutputStream("d:/2.txt",true);
		byte[] b = new byte[2];
		//返回值代表读了多少个有效字节，内容放在byte数组中。
		int len;
		while((len = fileIntputStream.read(b)) != -1){
			System.out.println(Arrays.toString(b));
			System.out.println(new String(b,0,len));
			fileOutputStream.write(new String(b,0,len,"gbk").getBytes());
		}
		fileIntputStream.close();
		fileOutputStream.close();
	}
```
