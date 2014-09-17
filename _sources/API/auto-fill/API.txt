查询自动填充
=============

URI: http://server/sf1r/auto_fill/index

当用户输入搜索关键词时，搜索框中显示以现有输入关键词为前缀的关键词列表。

- 输入参数：

* collection(字符串类型)：collection 名字。

* prefix(字符串类型)：查询前缀。

* limit(非负整数类型)：返回结果的数量，如果大于100，将取100，默认值为8.

- 输出参数：

* total_count(非负整数类型)：以指定前缀开头的关键词总数

* keywords(数组类型)：返回结果数组

  * name(字符串类型)：匹配到的关键词

  * count(非负整数类型)：匹配到的关键词在多少个文档中出现

- JSON请求的例子：

::

  {
    "collection":"example",    
    "prefix":"auto",
    "limit":10
  }

- JSON返回的结果：

::

  {
    "header": {"success": true},
    "total_count": 5 ,
    "keywords": [
      { "name" : "autocad", "count" : 20 } ,
      { "name" : "autodesk", "count" : 10 } ,
      { "name" : "autoexec", "count" : 8 } ,
      { "name" : "automatic", "count" : 15 } ,
      { "name" : "automobile", "count" : 12 }
    ]
  }


