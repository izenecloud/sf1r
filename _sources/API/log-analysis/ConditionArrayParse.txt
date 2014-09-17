条件
==========

条件是由若干过滤器构成的数组，数组包含属性名，操作符和操作数。过滤器之间的关系为"并"。

- 数组中元素的参数：

  property(字符串类型)：属性名

  operator(字符串类型)：操作符
    
    =：等于指定值

    <>：不等于指定值

    <：小于指定值

    >：大于指定值

    >=：大于或等于指定值

    between：在指定区间内

    in：在指定列表中

    not_in：不在指定列表中

    starts_with：以指定前缀开头

    ends_with：以指定后缀结尾

    contains：包含指定子串
  
  value(参数类型由属性名确定)：操作数。如果是数组，数组中的所有元素均被认为是操作符的参数，如果是NULL，则认为无参数；否则有1个参数

.. note:: 属性名必须是合法的过滤器。

一个条件的例子：

::
  
  [
    {"property":"price", "operator":"range", "value":[1.0, 10.0]},
    {"property":"category", "operator":"in", "value":["sports", "music"]},
    {"property":"rating", "operator":">", "value":3}
  ]

