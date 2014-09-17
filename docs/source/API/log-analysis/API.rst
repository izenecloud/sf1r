日志分析
============

从系统事件日志删除记录
--------------------------------

调用函数：log_analysis::delete_record_from_system_events

在系统事件日志中删除满足条件的记录。无返回结果。

- 输入参数：

  conditions(数组类型)：删除记录的 `条件`_

.. _条件: ConditionArrayParse.html

- JSON请求的例子：

::
  
  {
    "conditions":[
      {"property":"timestamp", "operator":"range", "value":[1.0, 10.0]},
      {"property":"level", "operator":"in", "value":["warn", "error"]}
    ]
  }


从用户请求日志中删除记录
-------------------------------

调用函数：log_analysis::delete_record_from_user_queries

在用户请求日志中删除中满足条件的记录。无返回结果。

- 输入参数：

  conditions(数组类型)：删除记录的 `条件`_
  
.. _条件: ConditionArrayParse.html

- JSON请求的例子：

::
  
  {
    "conditions":[
      {"property":"timestamp", "operator":"range", "value":[1.0, 10.0]},
      {"property":"level", "operator":"in", "value":["warn", "error"]}
    ]
  }

获取频繁用户请求
------------------------------------

调用函数：log_analysis::get_freq_user_queries_from_logserver

从日志服务器获取最频繁的用户请求。

- 输入参数：

  collection(字符串类型)：collection名字

  conditions(数组类型)：结果满足的 `条件`_

.. _条件: ConditionArrayParse.html

  sort(数组类型)：对结果进行 `排序`_

.. _排序: SortParse.html

  select(数组类型)： `选择`_ 输出的列

.. _选择: SelectParse.html

  groupby(字符串类型)：指定按哪个属性分组

  limit(非负整数类型)：指定返回请求的数量

- 输出参数：

  user_queries(对象类型)：所有满足条件的查询结果

- JSON请求的例子：

::

  {
    "collection":"example"
  }

- JSON返回的结果：

添加用户请求
-------------------

调用函数：log_analysis::inject_user_queries

添加用户请求。无返回结果

- 输入参数

  user_queries(数组类型)：包含以下属性的查询数组
    
    query(字符串类型)：关键字

    collection(字符串类型)：collection名字

    hit_docs_num(非负整数类型=0)：命中文档数

    page_start(非负整数类型=0)：索引起始页

    page_count(非负整数类型=0)：每页文档数

    duration(字符串类型=0)：持续时间，格式为HH:MM:SS.ffffffffff，ffffffffff为秒的小数部分，可以忽略

    timestamp(字符串类型=now)：时间戳，格式为YYYY-mm-dd HH:MM:SS.ffffffffff，如果不指定，则使用当前时间

- JSON请求的例子：

::

  {
    "query":"a",
    "collection":"example"
  }

添加用户请求到日志服务器
--------------------------------

调用函数：log_analysis::inject_user_queries_to_logserver

添加用户请求到日志服务器。无返回结果

- 输入参数

  user_queries(数组类型)：包含以下属性的查询数组
    
    query(字符串类型)：关键字

    collection(字符串类型)：collection名字

    hit_docs_num(非负整数类型)：命中文档数，默认为0

    page_start(非负整数类型)：索引起始页，默认为0

    page_count(非负整数类型)：每页文档数，默认为0

    duration(字符串类型)：持续时间，格式为HH:MM:SS.ffffffffff，ffffffffff为秒的小数部分，可以忽略，默认为0

    timestamp(字符串类型)：时间戳，格式为YYYY-mm-dd HH:MM:SS.ffffffffff，默认为当前时间

- JSON请求的例子：

::

  {
    "query":"a",
    "collection":"example"
  }

查询系统事件
-------------

调用函数：log_analysis::system_events

查询满足条件的系统事件。

- 输入参数：

  select(数组类型)： `选择`_ 输出的列

.. _选择: SelectParse.html

  conditions(数组类型)：结果满足的 `条件`_

.. _条件: ConditionArrayParse.html

  sort(数组类型)：对结果进行 `排序`_

.. _排序: SortParse.html

- 输出参数：

  system_events(对象类型)：所有满足条件的系统事件

- JSON请求的例子：

::

  {
    "select": ["source", "content", "timestamp"],
    "conditions": [
      {"property":"timestamp", "operator":"range", "value":[1.0, 10.0]},
      {"property":"level", "operator":"in", "value":["warn", "error"]}
    ],
    "sort": ["timestamp"]
  }

- JSON返回的结果：

::

  {
    "header": {"success": true},
    "system_events":[
      {"level":"error", "source":"SYS", "content":"Out of memory", "timestamp":"2010-Jul-16 18:11:38" }
    ]
  }

显示用户请求
------------

调用函数：log_analysis::user_queries

显示满足条件的用户查询。

- 输入参数：

  collection(字符串类型)：collection名字

  conditions(数组类型)：结果满足的 `条件`_

.. _条件: ConditionArrayParse.html

  sort(数组类型)：对结果进行 `排序`_

.. _排序: SortParse.html

  select(数组类型)： `选择`_ 输出的列

.. _选择: SelectParse.html

  groupby(字符串类型):指定按哪个属性分组

  limit(非负整数类型)：指定返回请求的数量

- 输出参数：

  user_queries(对象类型)：所有满足条件的查询结果

- JSON请求的例子：

::

  {
    " collection " : " b5mp ",
    " conditions " :[
      {
        " property " : " TIMESTAMP " ,
        " operator " : " >= " ,
        " value " :[ " 20120529 T000000 " ]
      },
      {
        " property " : " TIMESTAMP " ,
        " operator " : " <= " ,
        " value " :[ " 20120628 T235959 " ]
      },
      {
        " property " : " hit_docs_num " ,
        " operator " : " >" ," value " :[ 0 ]
      }
    ],
    " sort " :[ { " order " : " DESC " , " property " : " COUNT " } ] ,
    " select " :[
      { " property " : " query " } ,
      { " func " : " COUNT " , " property " : " query " }
    ],
    " groupby " :[ " query " ],
    " limit " :10
  }

- JSON返回的结果：

::

  {
    " header " : { " success " : true },
    " user_queries " : [
      { " count " : " 110 " , " query " : " iphone " } ,
      { " count " : " 95 " , " query " : " ipad " } ,
      { " count " : " 83 " , " query " : " apple " } ,
      { " count " : " 80 " , " query " : " nokia " } ,
      { " count " : " 75 " , " query " : " 手机 " } ,
      { " count " : " 72 " , " query " : " 笔记本 " } ,
      { " count " : " 69 " , " query " : " 搜索引擎 " } ,
      { " count " : " 65 " , " query " : " 推荐引擎 " } ,
      { " count " : " 60 " , " query " : " 用户搜索日志 " } ,
      { " count " : " 50 " , " query " : " 点评 " }
    ],
  }



