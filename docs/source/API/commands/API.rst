命令API
========

建立索引
---------

URI: http://server/sf1r/commands/index

对collection建立索引。无返回结果

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp",
    "document_count":1000
  }

重新构建查询功能
-----------------

URI: http://server/sf1r/commands/index_query_log

发送"index_query_log"命令，以重新构建collection全部与查询相关的功能。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp",
  }

重新构建挖掘功能
-----------------

URI: http://server/sf1r/commands/mining

发送"mining"命令，以重新构建collection全部与数据挖掘相关的功能。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp",
  }

优化索引
------------

URI: http://server/sf1r/commands/optimize_index

优化倒排索引，合并多个索引桶成为一个单桶。无返回结果

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp"
  }

