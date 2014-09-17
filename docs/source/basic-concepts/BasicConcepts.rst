基本概念
========

这一部分将对SF1R基本概念和数据组织进行介绍。

collection
----------------

SF1推荐引擎是基于open API的服务器，它将各个功能封装成一系列的API并开放给用户使用，
用户可以使用JSON串组装请求。下面给出发送JSON请求的例子：

::
    
    {
        " collection ": " example " ,
        " resource ": {
            " rec_type ": " BOE " ,
            " max_count ": 20 ,
            " USERID ": " user_001 "
        }
    }

对于上面的JSON请求，SF1会返回如下的结果：

::
    
    {
        " header " : { " success " : true } ,
        " resources " : [
        {
            " ITEMID " : " item_001 " ,
            " weight " : 0.9 ,
            " reasons " : [
                { " event " : " purchase " } ,
                { " ITEMID " : " item_011 " }
            ],
            " name " : " iphone " ,
            " link " : " www . shop . com / product / item_001 " ,
            " price " : " 5000 " ,
            " category " : " digital "
        },
        {
            " ITEMID " : " item_002 " ,
            " weight " : 0.8 ,
            " reasons " : [
                { " event " : " shopping_cart " } ,
                { " ITEMID " : " item_012 " }
            ],
            " name " : " ipad " ,
            " link " : " www . shop . com / product / item_002 " ,
            " price " : " 6000 " ,
            " category " : " digital "
        }
      ]
    }

在上面给出的例子中，可以看到SF1的每个请求都必须指定Collection字段。该字段是
SF1的一个非常重要的概念，类似与RDBMS表。
在SF1中, collection是中具有同种结构的文档集合，由一批SCD文件组成, SF1使用
collection作为基本构件，管理系统中各类文档集合。同时，SF1对每个collection的
操作都有相应的config文件指定。

.. note::
    config文件是文件名与collection名称相同的XML文件。

什么是SCD，格式
---------------

文档集合是构建搜索服务的第一步工作。 为了能够对文档进行检索，首先应该生成文档的索引数据。
SF1的索引数据是通过索引SCD格式的文档生成的。 
SCD是structured crawled document的缩写，SCD文档是一个文本文件，必须符合一定的规则才能被索引。

命名规则
~~~~~~~~

SF1R通过文件名验证SCD文件的有效性，其命名规则如下:

:: 

    B-<document collector ID(2Bytes)>-<YYYYMMDDHHmm(12Bytes)>-<SSsss(second,millisecond,5Bytes>-<I/U/D/R(1Bytes)>-<C/F(1Byte)>.SCD

任何不符合上述命名规则的文件都将被视为非法文件而丢弃。

I/U/D/R给出了SCD文档类型，各个类型意义如下所示：
    +------------+------------+------------+------------+
    |   I(nsert) |  U(pdate)  |  D(elete)  |   R(Type)  |
    +============+============+============+============+
    | 索引新文档 |  更新文档  |  删除文档  |   RType    |
    +------------+------------+------------+------------+

.. note::
    当I，U，D类型的文档同时存在时，将按照顺序：I，U，D依次进行索引。

下面给出SCD文件名示例:

::

    B-00-201302271306-31564-U-C.SCD

文件结构
~~~~~~~~

SCD文档是一个文本文件，由一系列的K-V对组成，K-V值均由string标示，下表给出了一个SCD结构的示例：

============  ======== ===============
   域名         类型       描述
============  ======== ===============
    ID         string  唯一的文档ID
   date        string  文档的创建日期
  title        string  文档标题
 subtitle      string  文档的副标题
  writer       string  作者的名字
writeremail    string  作者的邮箱地址
  content      string  文档内容
============  ======== ===============


.. note::
    SCD中每一个K-V对，遵循格式“<FiledName>Field Content”。

下面给出一个SCD文档的实例：

::

    <DOCID>#1#1166452314021
    <title>SomeTitle 1
    <writer>SomeAuthor 1
    <writeremail>SomeAuthor@gmail.com
    <content>.......
    <DOCID>#1#1166452314023
    ............

Insert 和 Update类型的SCD文档实例如下：

::

    <DOCID>#1#1166452314021
    <Date> 20030922203324
    <title> WISEnut changes the search engine landscape yet again
    <subtitle> WISEnut scores another deal with a major corporation
    <writer>Some Author
    <writeremail>SomeAuthor@gmail.com
    <content>WISEnut has agreed to provide search solution service to a leading credit card company in Korea. The service is expected to start at the beginning of the next month.

Delete类型的文档实例如下：

::
    
    <DOCID>#1#1166452116647
    <DOCID>#1#1166452314021

.. note::
    Delete 类型的SCD文档, 只需列出要删除的文档id。

数据文件目录
------------

这部分对SF1的数据文件进行介绍

数据文件目录结构如下：

::
    
    ./
    |---collection
        |---example
            |---collection-data 
            |---query-data
            |---scd
                |---index
                |---recommend
                    |---item
                    |---order
                    |---user
    |---config
        |---example.xml
        |---sf1config.xml
    |---log
    |---query-support

sf1目录结构介绍： 

* collection 目录下包含每个collection的目录，每个目录的名称为collection的名称。以example为例，该目录下存放有3  个子目录：collection-data, query-data, scd。

    * collection-data: 下面有两个子目录-default-collection-dir, default-recommend-dir, default-collection-dir      下面存放索引数据和挖掘数据，default-recommend-dir下面存放推荐数据。

    * query-data: 存放 query-recommend, autofill 等特性的数据。

    * scd: index子目录下存放预索引的scd文件。recommend子目录下有三个子目录： item, user,order. 分别存放item,       user, order相关的scd文件。

* config: 存放所有配置文件和结构定义文件。结构定义文件存放在schema子目录下。 配置文件包括两部分，一是系统配置  文件：sf1config.xml, 和collection相关的配置文件，如example.xml, 所有和collection相关的配置文件都放在该目录下。

* log: 存放sf1的日志文件。

* query-support: 存放和querycorrection，autofill等特性相关的数据。


