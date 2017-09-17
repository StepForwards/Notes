---
title: MySQL正则表达式
tags: mysql
grammar_cjkRuby: true
---
|模式|描述|
|-|-|
|^|匹配输入字符串的开始位置。如果设置了RegExp对象的Multiline属性，^也匹配'\n'或'\r'之后的位置。|
|$|匹配输入字符串的结束位置。如果设置了RegExp对象的Multiline属性，$也匹配'\n'或'\r'之前的位置。|
|\`|匹配'\n'之外的·任何单个字符。要匹配包括'\n'在内的任何字符，请使用'[.\n]'的模式。|
|[...]|字符集合。匹配所包含的任意一个字符。例如，'[abc]'可以匹配"plain"中的'a'。|
|[^...]|负值字符集合。匹配未包含的任意字符。例如，'[^abc]'可以匹配"plain"中的'p'。|
|p1\|p2\|p3|匹配p1或p2或p3。例如，'z\|food'能匹配"z"或"food"。'(z\|f)ood'则匹配"zood"或"food"。|
|\*|匹配前面的子表达式零次或多次。例如，zo\*能匹配"z"以及"zoo"。\*等价于{0，}。|
|+|匹配前面的子表达式一次或多次。例如，zo\*能匹配"zo"以及"zoo"，但不能匹配"z"。\*等价于{0，}。|
|{n}|n是一个非负整数。匹配确定n次。例如，'o{2}'不能匹配"Bob"中的'o'，但是能匹配"food"中的两个o。|
|{n,m}|m和n均为非负整数，其中n <= m。最少匹配n次且最多匹配m次。|

**实例**

了解以上的正则需求后，我们就可以更加自己的需求来编写带有正则表达式的SQL语句。以下我们将列出几个小实例(表名：person_tbl )来加深我们的理解：

查找name字段中以'st'为开头的所有数据：

``` sql
mysql> SELECT name FROM person_tbl WHERE name REGEXP '^st';
```
查找name字段中以'ok'为结尾的所有数据：

``` sql
mysql> SELECT name FROM person_tbl WHERE name REGEXP 'ok$';
```
查找name字段中包含'mar'字符串的所有数据：

``` sql
mysql> SELECT name FROM person_tbl WHERE name REGEXP 'mar';
```
查找name字段中以元音字符开头或以'ok'字符串结尾的所有数据：

``` sql
mysql> SELECT name FROM person_tbl WHERE name REGEXP '^[aeiou]|ok$';
```
