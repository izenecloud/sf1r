collection日常维护与管理
=========================

检查
-----

URI: http://server/sf1r/collection/check

在分布式系统中对collection进行一致性检查。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字。

- JSON请求的例子：

::

  {
    "collection":"b5mp"
  }

创建
-----

URI: http://server/sf1r/collection/create

创建collection。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

* collection_config(字符串类型)：配置信息，即对应.xml文件的内容

- JSON请求的例子：

::

  {
    "collection":"b5mp"
    "collection_config":"xxxxxx"
  }

删除
------

URI: http://server/sf1r/collection/delete

删除collection。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp"
  }


载入许可证
------------

URI: http://server/sf1r/collection/load_license

载入许可证，仅对企业版本有效。无返回结果。

- 输入参数：

* path(字符串类型)：许可证的路径

- JSON请求的例子：

::

  {
    "path": "/home/customer/sf1_license/license_key.dat"
  }

重新构建索引
------------------

URI: http://server/sf1r/collection/rebuild_collection


清除collection中已删除的文档并重建collection。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp"
  }

从SCD重建collection
----------------------

URI: http://server/sf1r/collection/rebuild_from_scd

删除所有旧的数据，从scd文件重建collection，需要将完整的scd文件放在指定位置。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp"
  }

停止collection
---------------

URI: http://server/sf1r/collection/stop_collection

停止collection。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp"
  }

更新配置信息
--------------

URI: http://server/sf1r/collection/update_collection_conf

以相应的.xml文件更新collection的配置。无返回结果

- 输入参数：

* collection(字符串类型)：collection名字

- JSON请求的例子：

::

  {
    "collection":"b5mp"
  }

