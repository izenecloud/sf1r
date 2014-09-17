属性排序
==========

**Sort by**

按属性排序是指对检索结果按照某个属性实现升序或者降序排列。一般可以对以下类型属性进行排序：

**数字类型** 

如：价格Price, 销量Sale，评论数，等;该属性必须是filter，配置如下： 

::

	<Property name="Score">
	  <Indexing filter="yes" multivalue="no" doclen="no" tokenizer="" rankweight="0.1" />

**时间类型** 

如：上架时间，结束时间，等； 其配置如下：

::

	<Property name="CreateTime">
          <Indexing filter="yes" multivalue="no" doclen="no" />

**字符串类型**  

如：按商品型号排序，等；其配置如下： 

::

	<Property name="Link">
	  <Indexing filtering = "yes" multivalue = "no" rtype = "yes">

**排序方式** 

有两种排序方式：升序(ASC)，降序(DESC), 在检索request中添加：

::

	"sort":[ { "order":"DESC", "property":"COUNT" }]
