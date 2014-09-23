文档操作API
============

创建文档
---------

URI: http://server/sf1r/documents/create

根据输入的参数创建一个文档，必须指定DOCID，无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字
* resource(对象类型)：创建文档内容，为一系列属性名和属性值的键值对:

  * property(字符串类型)：属性

  * value(参数类型由属性名确定)：属性的值
  
- JSON请求的例子：

::

  {
    "collection":"example",
    "resource":{
      "DOCID":"post.1",
      "Title":"Hello,World",
      "Content":"This is a test post."
    }
  }

.. note::
    如果SF1配置为实时索引模式，那么调用一次该操作后，将根据文档的Schema配置建立实时索引（倒排索引），如果没有配置实时索引模式，那么文档累积到一定程度后会建立索引，该值可配置。该API为异步模式，故无返回。

删除文档
---------

URI: http://server/sf1r/documents/destory

删除一个文档。无返回结果

- 输入参数

* collection(字符串类型)：collection名字

* resource(字符串类型):对文档的描述

  * DOCID(字符串类型)：删除DOCID对应的文档

- JSON请求的例子：

::

  {
    "resource":{
      "DOCID":"post.1",
    }
  }

更新文档
--------

URI: http://server/sf1r/documents/update

更新一个文档，必须指定DOCID。无返回结果。

- 输入参数：

* collection(字符串类型)：collection名字

  * resource(对象类型)：对文档的描述
  
    * property(字符串类型)：文档的属性
    
    * value(any)：属性的值

- JSON请求的例子：

::

  {
    "resource":{
      "DOCID":"post.1",
      "title":"Hello,World (revision)",
      "content":"This is a revised test post."
    }
  }

.. note::
    如果SF1配置为实时索引模式，那么调用一次该操作后，将根据文档的Schema配置建立实时索引（倒排索引），如果没有配置实时索引模式，那么文档累积到一定程度后会建立索引，该值可配置。该API为异步模式，故无返回。更新文档相当于删除旧文档和添加新文档两个操作的组合。


原地更新文档
--------------

URI: http://server/sf1r/documents/update_inplace

原地更新一个文档而无需传递文档的原始内容。该操作常用于更新频繁的字段，例如计数器。该字段必须在Schema中配置为RType字段，既数值类型+filter，属性值为空则无法更新。无返回结果

- 输入参数：

* collection(字符串类型)：collection名字

* resource(对象类型)：对文档的描述
  
 * DOCID(字符串类型)：文档的DOCID

 * update(数组类型)：更新的内容

   * op(字符串类型)：更改操作，目前只支持add

   * property(字符串类型)：更改的属性，只能为数值型的属性，且可作为过滤器。

   * opvalue(字符串类型)：改变的值

- JSON请求的例子：

::

  {
    "resource":{
      "DOCID":"post.1",
      "update":[
      {"op":"add","property":"price","opvalue":"10},
      {"op":"add","property":"count","opvalue":"2"}
      ]
    }
  }

获取文档
---------

URI: http://server/sf1r/documents/get

根据用户输入条件获取文档。

- 输入参数

* collection(字符串类型)：collection名字

* select(数组类型)： 选择输出的文档属性，如果为空则输出全部属性

* conditions(数组类型)：根据条件筛选结果 ，但是operator只能为=或in，property只能为_id或DOCID或其他可作为过滤器的属性,当property为uuid(b5mo)或DOCID(b5mp)时，id_type可以选择isbn和url,当id_type为isbn，value为书籍的isbn号时，可以获得该书籍的doc文档，当id_type为url时，value为商品的url时,可以获得该商品的doc文档
  

- 输出参数

* resources(数组类型)：满足条件的文档

  * property(字符串类型)：属性

  * value(数据类型由属性确定)：属性对应的值

  * total_count(非负整数类型)：满足条件文档的数量

- JSON请求的例子：

::

  {
    "collection": "example",
    "conditions": [
      {"property": "DOCID", "operator": "=", "value": "post.1",id_type="isbn"}
    ],
    "select": [
      { "property": "Title" },
      { "property": "Content"}
    ]
  }

- JSON返回的结果：

::

  {
    "header": {"success": true},
    "resources":
    [
      {
        "Content":"This is a test post.",
        "Title":"hello,world",
        "_id":1
      }
    ],
    "total_count":1
  }

.. note::
    DOCID是文档的主键，其对应内部属性为_id，因此根据这2个属性获取文档，都是主键查询； 如果conditions里的property填写的是其他字段，查询速度则慢于根据主键查询的速度。



获取文档数量
-------------

URI: http://server/sf1r/documents/get_doc_count

获取collection中已建立索引的文档数量。

- 输入参数：

* collection(字符串类型)：collectin名字

- 输出参数：

* count(非负整数类型)：被索引文档的数量

- JSON请求的例子：

::

  {
    "collection":"example"
  }

- JSON返回的结果：

::

  {
    "header": {"success": true},
    "count":1
  }


