选择
======

指明选择哪些属性输出，并且指定输出的格式。

- 数组中元素的参数：

  property(字符串类型)：属性名

  highlight(布尔类型)：查询关键字是否高亮，默认false

  snippet(布尔类型)：是否返回片段，false表示返回全文，默认false

  summary(布尔类型)：是否返回对属性的说明，默认false

  summary_setence_count(非负整数类型)：属性说明最多包含多少个句子，默认3

  summary_property_alias(字符串类型)：属性说明的别名，默认为属性名+".summary"

  split_property_value(布尔类型)：是否将属性值划分为多个值，默认false

.. note:: 

  如果属性内容只是单个字符串，则只需指定参数property，例如{"property":"title"}。

  如果select数组为空，那么只会返回内部属性如"_id"。

  如果没有指定select，那么会返回文档模式中的所有属性。

  如果文档模式中出现了ACL_ALLOW和ACL_DENY，那么在任何情况下都会返回这两个参数。

