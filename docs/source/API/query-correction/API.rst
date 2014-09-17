查询自动纠错
================

URI: http://server/sf1r/query_correction/index

纠正用户输入的错误的查询关键词。

- 输入参数：

* collection(字符串类型)：collection 名字。

* keywords(字符串类型)：查询关键词。

- 输出参数：

* refined_query(字符串类型)：更正后的查询

- JSON请求的例子：

::

  {
    "collection":"example",
    "keywords":"shanghai yiyao"
  }

- JSON返回的结果：

::

  {
    "header": {"success": true},
    "refined_query":"上海 医药"
  }

