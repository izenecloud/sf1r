结果展现
========

SF1使用select数组指定哪些属性将会包含在Response中以及每个
属性的展示方式。
下面给出select的示例：

::
    
      "select":
      [
        {
            "property":"Title",
            "highlight":true,
            "snippet":true,
            "summary":true,
            "summary_sentence_count":3
        }
       ]


select支持的各个参数解释如下：

* proeprty（String类型）：将会包含在Response中的属性名称。
* highlight（Bool类型）：是否在Response中高亮显示搜索关键词。
* snippet（Bool类型）：是否使用分页显示
* summary（Bool类型）：是否增加摘要信息
* summary_setence_count（Uint类型）：摘要的最大句子数目
* summary_property_alias（String类型）：摘要属性的别名。默认使用属性名称加上 kDefaultSummaryPropertySuffix的后缀
* split_property_value（Bool类型）：是否将属性值分割成多个值显示。

.. note::
    如果select数组为空，只有内部属性（_id等）返回至Response中。
    如果没有指定select，则所有在document schema中指定的属性都会
    返回至Response。

需要特别说明的是split_property_value属性。

* 如果在<MiningBundle><Schema><Group>中配置了该属性。则：
  对于原始属性值：
  ::
    
    "数码>手机通讯>手机,苹果商城>iPhone"
   

  分割结果为：

  ::
        
      [
         ["数码", "手机通讯", "手机"],
         ["苹果商城", "iPhone"],
      ]
* 如果在<MiningBundle><Schema><Atrr>中配置了该属性，则：
  对于原始属性值：
  
  ::
    
     "领子:圆领,尺码:S|M|L|XL"
     
  分割结果为：

  ::
    
     [
        {"attr_name": "领子", "attr_values": ["圆领"]},
        {"attr_name": "尺码", "attr_values": ["S", "M", "L", "XL"]}
     ]
 
* 任何其他配置，该属性均不会被分割。

