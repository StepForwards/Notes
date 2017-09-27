---
title: Lucene
tags: search
grammar_cjkRuby: true
---

**数据的分类**

> 我们生活中的数据总体分为两种：结构化数据和非结构化数据。
**结构化数据**：指具有固定格式或有限长度的数据，如数据库，元数据
等。
**非结构化数据**：指不定长或无固定格式的数据，如邮件，word文档等磁盘上的文件

**结构化查询**

> 因为数据库等这种结构化数据存储是有规律的，有行有列而且数据格式、数据长度都是固定的,所以使用sql语句进行查询,就能很快的查询到结构

**非结构数据查询**

> 非结构数据查询方式有两种
1.**顺序扫描法**(Serial Scanning)比如要找内容包含某一个字符串的文
件，就是一个文档一个文档的看，对于每一个文档，从头看到尾，如果此文档包含此字符串，则此文档为我们要找的文件，接着看下一个文件，直到扫描完所有的文件。如利用windows的搜索也可以搜索文件内容，只是相当的慢。
2.**全文检索**(Fulltext
Search)将非结构化数据中的一部分信息提取出来，重新组织，使其变得有一定结构，然后对此有一定结构的数据进行搜索，从而达到搜索相对较快的目的。这部分从非结构化数据中提取出的然后重新组织的信息，我们称之索引。

**全文检索**

> 类似于字典的过程,字典的拼音表和部首检字表就相当于字典的索引,我们通过索引查找到我们需要的内容这种先建立索引，再对索引进行搜索的过程就叫全文检索(FulltextSearch)。

**Lucene**

> Lucene是apache下的一个开放源代码的全文检索引擎工具包。提供了完整的查询引擎和索引引擎，部分文本分析引擎。我们可以使用Lucene实现全文检索的功能

- 下载地址http://lucene.apache.org/
- 创建索引查询索引的流程
 
 ![索引创建查询][1]

**创建索引**

> 对文档索引的过程，将用户要搜索的文档内容进行索引，索引存储在索引库(index),春关键索引的过程包括,几个部分,**1.获取原始文档2.创建文档对象进行分析3.创建索引放入索引库**

**获取原始文档**

> 原始文档是指要索引和搜索的内容。原始内容包括互联网上的网页、数据库中的数据、磁盘上的文件等。Lucene不提供信息采集的类库，需要自己编写一个爬虫程序实现信息采集，也可以通过一些开源软件实现信息采集,常用的java的信息采集类库有Nutch,jsoup,heritrix,我们目前研究的对象,是磁盘中的文件

**创建文档对象**

> 获取原始内容的目的是为了索引，在索引前需要将原始内容创建成文档Document对象,文档中包括一个一个的域Field域中存储内容。

![文档对象][2]

- 我们可以为磁盘中的每一个文件当成一个document对象,中包括一些
	- Field如我们可以指定
	- file_name文件名称
	- file_path文件路径
	- file_size文件大小
	- file_content文件内容

- 每个document可以有多个Field
- 不同的document可以有不同的field
- 同一个document可以定义相同的Field
- 每一个文档放入索引库的时候都会生成一个唯一的编号,叫做id,id和数据库不同的是id不是域

**分析文档**

> 将原始内容创建为包含域（Field）的文档（document），需要再对域中的内容进行分析，分析的过程是经过对原始文档提取单词、将字母转为小写、去除标点符号、去除停用词等过程生成最终的语汇单元，可以将语汇单元理解为一个一个的单词。

- 例如我们通过这段话==The Spring Framework provides a comprehensive programming and configuration model for modern Java-based enterprise applications - on any kind of deployment platform. A key element of Spring is infrastructural support at the application level: Spring focuses on the "plumbing" of enterprise applications so that teams can focus on application-level business logic, without unnecessary ties to specific deployment environments==
经过分析就成为一个语汇单元==spring,framework,comprehensive,programming.......==
- 每一个单词叫做一个Term,不同的域(如文件名,文件路径,文件内容)都含有同相同的单词(如spring)会被认为是不同的Term
- Term中包含两个部分
	- 一部分是文档的域名
	- 一部分是单词的内容
- 每一个Term对象会被创建为索引保存到我们的索引库中
- 当我们进行搜索的时候,本质就是创建一个Term,包含域名和单词内容

![document][3]

**创建索引**
- 索引库由两个部分组成索引和文档对象
- 索引就是我们之前进行文件分析的时候的每个Term对象
- 文档对象就是我们之前创建的document对象
- 如果此时有多个文档对象有相同的Term对象,那么在索引库中只会有一个Term,在此Term中会有一个文档对应的编号,用来指定当前Term对应哪个文档,当进行查询的时候会将对应的多个文档一并返回
- 如果一个Term对应多个文档,那么这多个文档的先后顺序按照文档中的出现次数作为排序,次数越多排序越靠前

**倒排索引**

> 正常查看文档的方式应该先找到文档,再去查找文档中的内容,而我们上述的过程是先通过查找文档中的内容生成索引,再通过索引去找到对应的文档,这种方式叫做倒排索引.倒排索引结构也叫反向索引结构，包括索引和文档两部分，索引即词汇表，它的规模较小，而文档集合较大,所以通过倒排索引的方式速度较快

**创建索引API**

- 导包

![所需jar包][4]

- 创建indexwriter对象
	- 指定索引库的存放位置Directory对象`FSDirectory fsd = FSDirectory.open(new File("src/com/forward/lucene/FSDirectory"));`
	- 指定一个分析器,对文档内容进行分析`Analyzer analyzer = new StandardAnalyzer();`
	- 创建indexwriter配置对象,指定lucene的版本号和分词器`IndexWriterConfig iwc = new IndexWriterConfig(Version.LATEST, analyzer);`
	- 向索引库中添加索引`IndexWriter iw = new IndexWriter(fsd, iwc);`
- 创建文档对象`Document doc = new Document();`
- 创建Field对象`Field fileNameField = new TextField("fileName", fileName, Store.YES);`
- IDfield对象添加到document对象中`doc.add(fileNameField);`
- 使用indexwrite对象将document对象写入索引库,此过程进行索引创建.并将索引和document对象写入索引库`iw.addDocument(doc);`
- 关闭IndexWriter对象`iw.close();`

``` java
public class LuceneDemo {
	
	@Test
	public void test01() throws Exception{
		//创建索引库
		FSDirectory fsd = FSDirectory.open(new File("src/com/forward/lucene/FSDirectory"));
		//创建分词器,分词器是抽象类,用这个类的子类创建
		Analyzer analyzer = new StandardAnalyzer();
		//indexwrite配置对象,指定lucene的版本号和分词器
		IndexWriterConfig iwc = new IndexWriterConfig(Version.LATEST, analyzer);
		//向索引库中添加索引
		IndexWriter iw = new IndexWriter(fsd, iwc);
		
		//创建File 路径为资源目录
		//获取该路径效的所有File对象
		//遍历数组
		//每次提取文件名 提取路径 提取内容 提取大小
		File path = new File("D:/Develop/Order/Lucene/lucene/source");
		File[] listFiles = path.listFiles();
		for(File file : listFiles){
			//获得文件属性
			String fileName = file.getName();
			String filePath = file.getAbsolutePath();
			String fileContent = FileUtils.readFileToString(file);
			long fileSize = FileUtils.sizeOf(file);
			
			//创建文档对象
			Document doc = new Document();
			
			//创建Field域
			Field fileNameField = new TextField("fileName", fileName, Store.YES);
			Field fileContentField = new TextField("fileContent", fileContent, Store.YES);
			Field filePathField = new StoredField("filePath", filePath);
			Field fileSizeField = new LongField("fileSize", fileSize, Store.YES);
			
			//将Field域添加到文档对象
			doc.add(fileNameField);
			doc.add(fileContentField);
			doc.add(filePathField);
			doc.add(fileSizeField);
			
			//写入索引及文档
			iw.addDocument(doc);
		}
		iw.close();
		
	}
}

```

**Field域的属性**

> 对于Field域,有些内容是需要进行分析的而有些内容是不需要进行分析的,如文件的内容是需要进行分词处理的,而文件的路径是不需要进行分词处理的,有些内容是需要建立索引的,有些内容则不需要建立索引,如名称,规格等需要建立索引,等等信息都是我们可以进行配置的

- 是否分析：是否对域的内容进行分词处理。前提是我们要对域的内容进
行查询。
- 是否索引：将Field分析后的词或整个Field值进行索引，只有索引方可
搜索到。比如：商品名称、商品简介分析后进行索引，订单号、身份证号不用分析但也要索引，这些将来都要作为查询条件。
- 是否存储：将Field值存储在文档中，存储在文档中的Field才可以从Document中获取比如：商品名称、订单号，凡是将来要从Document中获取的Field都要存储。
- 不同Field的子类具有的功能不同

|Field类|数据类型|Analyzed是否分析|Indexed是否索引|Stored是否存储|说明|
|-|-|-|-|-|-|
|StringField(FieldName,FieldValue,Store.YES))|字符串|N|Y|Y或N|这个Field用来构建一个字符串Field，但是不会进行分析，会将整个串存储在索引中，比如(订单号,姓名等)是否存储在文档中用Store.YES或Store.NO决定|
|LongField(FieldName,FieldValue,Store.YES)|Long型|Y|Y|Y或N|这个Field用来构建一个Long数字型Field，进行分析和索引，比如(价格)是否存储在文档中用Store.YES或Store.NO决定|
|TextField(FieldName,FieldValue,Store.NO)或TextField(FieldName,reader)|字符串或流|Y|Y|Y或N|如果是一个Reader,lucene猜测内容比较多,会采用Unstored的策略,例如文件名称|

**使用Luke工具查看索引文件**

- 找到Luke目录,双击start.bat进行打开

![start.bat][5]

![选中索引库][6]

![索引库信息][7]

![索引信息][8]


  [1]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506510392022.jpg
  [2]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506510585576.jpg
  [3]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506511005005.jpg
  [4]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506511218327.jpg
  [5]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506512754666.jpg
  [6]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506512789912.jpg
  [7]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506512843811.jpg
  [8]: https://www.github.com/StepForwards/my-notes/raw/images/Lucene/images/1506512889686.jpg