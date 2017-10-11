> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 http://www.cnblogs.com/langgj/p/6595756.html

# 在本次操作中所用到的命令

1\. 首先启动 HDFS

$HADOOP_HOME/sbin/start-dfs.sh

2\. 关防火墙

切换到 root 用户，执行 service iptables stop

3\. 拷贝文件到 HDFS

bin/hadoop fs -put 本地 HDFS

4\. 查看 HDFS 根目录的文件

bin/hadoop fs -ls /

# 1\. 新建 Java 项目，导入 Hadoop 相关 jar 包。

在 hadoop 解压包中的 hadoop-2.6.0\share\hadoop\common 目录下红色标注的文件全部拷贝

![](http://images2015.cnblogs.com/blog/919267/201703/919267-20170321172336846-1240077800.png)

在 hadoop-2.6.0\share\hadoop\hdfs 目录下红色标注的文件全部拷贝

![](http://images2015.cnblogs.com/blog/919267/201703/919267-20170321172437346-1956437403.png)

然后在 Java 项目中构建配置路径

# 2\. 编写代码

<pre>    FileSystem fileSystem;

    /*
     * 初始化
     */
    @Before
    public void init() throws Exception{
        //读取数据由平台上的协议确定
        URI uri = new URI("hdfs://192.168.*.*:9000");
        Configuration conf = new Configuration();
        fileSystem = FileSystem.get(uri, conf);
    }

    /*
     * 查看目录
     */
    @Test
    public void Catalog() throws Exception{
        Path path = new Path("/poker");
        FileStatus fileStatus = fileSystem.getFileStatus(path);
        System.out.println("*************************************");    
        System.out.println("文件根目录: "+fileStatus.getPath()); 
        System.out.println("这文件目录为：");
        for(FileStatus fs : fileSystem.listStatus(path)){ 
            System.out.println(fs.getPath()); 
        } 
    }

    /*
     * 浏览文件
     */
    @Test
    public void look() throws Exception{
        Path path = new Path("/core-site.xml");
        FSDataInputStream fsDataInputStream = fileSystem.open(path);
        System.out.println("*************************************");
        System.out.println("浏览文件：");
        int c;
        while((c = fsDataInputStream.read()) != -1){
            System.out.print((char)c);
            }
        fsDataInputStream.close();
    }

    /*
     * 上传文件
     */
    @Test
    public void upload() throws Exception{
        Path srcPath = new Path("C:/Users/Administrator/Desktop/hadoop/hadoop.txt");  
        Path dstPath = new Path("/");  
        fileSystem.copyFromLocalFile(false, srcPath, dstPath);
        fileSystem.close(); 
        System.out.println("*************************************");
        System.out.println("上传成功！");
    }

    /*
     * 下载文件
     */
    @Test
    public void download() throws Exception{
        InputStream in = fileSystem.open(new Path("/hadoop.txt"));  
        OutputStream out = new FileOutputStream("E://hadoop.txt");  
        IOUtils.copyBytes(in, out, 4096, true);  
    }

    /*
     * 删除文件
     */
    @Test
    public void delete() throws Exception{
        Path path = new Path("hdfs://192.168.*.*:9000/hadoop.txt");
        fileSystem.delete(path,true);
        System.out.println("*************************************");
        System.out.println("删除成功！");
    }</pre>

# 3\. 运行时发现出现用户没有权限的错误。

解决方法：

**1\. 修改 HDFS 根目录的权限**

**2\. 把 Hadoop 权限验证关闭，把 hadoop.dll 文件放到 C:/windows/system32 中，然后修改 hdfs-site.xml 文件，把验证关闭**

<property>

     <name>dfs.permissions</name>

     <value>false</value>

</property>

**3\. 伪造用户 -DHADOOP_USER_NAME = 用户名**

**![](http://images2015.cnblogs.com/blog/919267/201703/919267-20170321174305690-28201825.png)**