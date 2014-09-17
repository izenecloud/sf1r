查询分组-GroupBy Search
========================

查询结果分组是SF1搜索引擎的groupby特性，顾名思义，
就是按照某个或某些属性值，将搜索结果分组。

配置文件
----------

如果需要基于某个属性对搜索结果进行分组（该属性的类型必须为string，
int，float或datetime），必须在配置文件的MiningBundle/Schema/Group
项对属性设置。

此外，如果该属性是int或者float，还需要将该属性配置为filter（在配置
文件的IndexBundle部分）。

例如：

::

    < DocumentSchema >
    ...
    < Property name = " Category " type = " string " / >
    < Property name = " Price " type = " float " / >
    < Property name = " PlayTime " type = " datetime " / >
    </ DocumentSchema >
    < IndexBundle >
        < Schema >
        < Property name = " Price " >
            < Indexing filter = " yes " multivalue = " no " doclen = " yes " tokenizer = " "
    rankweight = " 0.1 " / >
        </ Property >
        ...
        </ Schema >
    </ IndexBundle >
    < MiningBundle >
        < Schema >
        ...
        < Group >
            < Property name = " Category " / >
            < Property name = " Price " / >
            < Property name = " PlayTime " / >
        </ Group >
        </ Schema >
    </ MiningBundle >


SCD格式
-------

如果groupby属性字段的数据类型是string，那么该字段可以分为多层属性。
下面是该属性的SCD格式：

::
    
    <PropertyName>A>B>C,D>E>F...

* PropertyName表示需要对搜索结果进行groupby的属性名称
* 该属性可以包含多个值，每个值之间用”，“或者”；“隔开。
* 如果该属性有parent值，需要给出完整的路径，路径中间以”>“隔开。
* 如果在某些属性值中，存在”，“，”；“，”>“，或者双引号号时，该属性必须
  用双引号引起来。对于属性值中包含双引号的情况，每个双引号必须重复写2次。

例如：
某属性值包含3级：

* 1级的值为John，Mark

    * 2级的值为1+1>2

        * 3级的值为”Mary“

该属性值的格式为：

::
    
    “John,Mark”>“1+1>2”>“ “ “Mary” ” ”

 
Categroy字段实例：

 ::
    
    <Categroy>创意生活，办公电脑>网络设备，手机数码>手机
    “John,Mark”>“1+1>2”>“ “ “Mary” ” ”

在该例子中，Categroy的属性值层次结构为：

==========      =========        ============
Level 1         level 2          level 3
==========      =========        ============
创意生活
电脑办公        网络设备
手机数码        手机通信         手机
John,Mark       1+1>2            "Mary"
==========      =========        ============

获取分组结果的API
--------------------

API的使用参见
:doc:`../../API/search/API`

下面给出按某些属性对搜索结果分组的Request：

::
    
    {
        " collection " : " example " ,
        " search " : {
            " keywords " : " test "
            " in " : [ " Title " , " Content " ]
        },
        " select " : [ " Title " , " Content " ] ,
        " group " : [
            { " property " : " Category " } ,
            { " property " : " Price " }
        ]
    }

分组结果在Response的group数组中，其中每个label为一个属性的分组结果：

::
    
    " group " : [
        { " docume nt_cou nt " : 100 ,
          " property " : " Category " ,
          " labels " : [
            { " label " 厨房电器" ,
              " do cument _count " : 7 ,
              " sub_labels " : [
                { " label " : "豆浆机" ," docu ment_c ount " : 5 }
              ]
            }
           ...
          ]
        },
        { " docume nt_cou nt " : 100 ,
            " property " : " Price " ,
            " labels " : [
                { " label " : " 100.0 " ," docu ment_c ount " : 4} ,
                { " label " : " 200.0 " ," docu ment_c ount " : 6} ,
                ...
            ]
        }
    ]


Select label API - 选择某一级属性值，得到该属性值下的所有搜索结果
------------------------------------------------------------------

在获取到搜索结果的分组结果的基础上，用户可以选择其中一个分组，
得到该分组的所有商品信息，该特性对用户起导航的作用。

例如，选取前面的”厨房电器“这一分组。相应的Requset如下：

::
    
    { 
        " collection " : " example " ,
        " search " : {  
            " keywords " : "豆浆机" ,
            " in " : [ " Title " , " Content " ] ,
            " group_label " : [
                { " property " : " Category " ,
                  " value " : [ "厨房电器" ] } ,
                { " property " : " Price " ,
                  " value " : [ " 100.0 " ]}
            ]
        },
        " select " : [ " Title " , " Content " ] ,
        " group " : [
            { " property " : " Category " } ,
            { " property " : " Price " }
        ]
    }

使用该功能时：
    * 在search/group_label里，指定某一级属性值
    * property是groupby的属性名称
    * value是所选择的属性值，为数组类型，存放从root
      节点到该级的全路径。value[0]是一级节点值，value[1]是二级节点值
      ,..,value[last]是叶子节点值。
    * 可指定多个lable。例如，有4个lable，分别是A1，A2，B1，B2。如果
      属性值”A1“，”A2“对应的属性为”A“，属性值”B1“，B2”对应的属性为“B”，
      那么搜索返回的结果符合条件（"A1" or "A2"） and（"B1" or "B2"）。

 
获取区间分组结果API--Group by Range
--------------------------------------

用户可以得到区间形式的分组结果，主要是针对价格这种类型的属性。
该特性只支持int或者float类型的属性。

下面给出Request的例子：

::
    
    {
        " collection " : " ChnWiki " ,
        " search " : {
            " keywords " : " test ",
            " in " : [ " Title " ," Content " ]
        },
        " select " : [ " Title " ," Content " ] ,
        " group " : [
            { " property " : " Price " ,
            " range " : true }
        ]
    }

在group参数中，需要设置进行区间分组的属性名称，
如Price和Range参数值为true，上例是搜索关键词“test”
时对搜索结果按价格区间分组。


下面是该Requset的返回结果，其中label字段中是每个
分组区间的值，值的格式是“start-end”。而document_count
字段则给出了相应的文档数目。

::
    
    " group " : [
     { " docu ment_c ount " : 100 ,
       " property " : " Price " ,
       " labels " : [
            { " label " : " 0 -99 " ,
              " do cument _count " : 4} ,
            { " label " : " 100 -199 " ,
              " do cument _count " : 6} ,
            ...
      ]
     }
    ]

Select range label-选择某一区间，得到该区间下的所有搜索结果
------------------------------------------------------------

在获取到搜索结果的区间分组的基础上，用户可以选取其中的
一个区间，得到该区间中的所有商品信息，该特性对用户有很
好的导航作用。

下面给出Request的例子

::
    
    {
        " collection " : " ChnWiki " ,
        " search " : {
            " keywords " : " test " ,
            " in " : [ " Title " , " Content " ] ,
            " group_label " : [
                { " property " : " Price " ,
                  " value " : [ " 100 -200 " ]}
            ]
        },
        " select " : [ " Title " , " Content " ]
    }

各个参数意义如下：

* search/group_label：在该参数中设置属性名称和相应的该属性的区间值。
* 属性区间值的格式是“start-end”。也可以指定为“-end”格式，表示小于
  等于end；反之亦然。
* property参数是group_label中的属性名称。但是选择某个区间的同时，不能
  同时在group参数里指定该属性，因为区间结果是动态生成的。


Group by Sub-Property - 子属性分组
-----------------------------------

在属性分组特性中，用户不仅可以选择按属性分组，还可以进一步指定按照
该属性的子属性分组。

例如，可以在Groupby属性中指定“Source”，并进一步指定子属性“Score”，
相应的Request如下：

::
    
    {
        "collection": "ChnWiki",
        "search": {
            "keywords": "test"，
            "in": ["Title", "Content"]
        },
        "select": ["Title", "Content"],
        "group": [
            { "property": "Source",
               "sub_property": "Score" 
            }
        ]
     }    

Response如下：

::
    
    "group": [
    { "document_count": 100,
      "property": "Source",
      "labels": [
      { "label": "京东",
        "document_count": 7,
        "sub_labels": [
          { "label": "3",
            "document_count": 5 },
          { "label": "4",
            "document_count": 2 }
        ]
      },
      { "label": "当当",
        "document_count": 10,
        "sub_labels": [
          { "label": "4",
            "document_count": 6 },
          { "label": "5",
            "document_count": 4 }
        ]
      },
      ...
      ]
     }
    ]

Group by date - 按时间分组
-------------------------------

SF1支持对查询结果按时间分组，只需在group中指定
datetime属性即可。

按时间分组需要SCD文档包含Playtime属性，例如

::
    
    <PlayTime>20120717193000;20120718193000;20120719193000

从该例子可以看出Playtime的几点特性：

* 对个时间戳之间通过“，”或“；”分开
* 每个时间戳遵循如下格式：
  
  ::
      
      YYYYMMDDhhmmss

   
解释如下：
   =======   ========
   符号      意义
   =======   ========
   yyyy      年
   MM        月
   DD        日
   hh        时
   mm        分
   ss        秒
   =======   ========

下面给出Request和Response的例子：

Request:

::
    
    {
        "collection": "ChnWiki",
        "search": {
            "keywords": "test"，
            "in": ["Title", "Content"]
        },
        "select": ["Title", "Content"],
        "group": [
            { "property": "PlayTime",
              "unit": "M" }
        ]
    }

    
上例中unit属性指定了Response返回结果的格式，意义如下：
    =======   =========  ==============
    unit值    意义       示例
    =======   =========  ==============
    “Y”       年         “2012”
    “M”       月         “2012-07”
    “D”       日         “2012-07-17”
    =======   =========  ==============

Response:

::
    
    "group": [
    { "document_count": 100,
      "property": "PlayTime",
      "labels": [
        { "label": "2012-07",
          "document_count": 4 },
        { "label": "2012-08",
          "document_count": 6 },
        ...
      ]
    } 
    ]


Select Data Label

* 用户可以在group_label里指定date，这样返回的文档就属于
  特定的标签
* group_label/value的格式定义如下：

=====================    ================
group_label/value示例    可能的返回结果
=====================    ================
["2012"]                 2012年
["201-07"]               2012年7月
["2012-07-17"]           2012年7月12日
=====================    ================


下面给出月份标签的请求示例：

::
    
    {
        "collection": "ChnWiki",
        "search": {
            "keywords": "test",
            "in": ["Title", "Content"],
            "group_label": [
                { "property": "PlayTime",
                  "value": ["2012-07"] }
            ]
        },
        "select": ["Title", "Content"]
     }


Auto Select Top Label - 顶级标签智能选取特性
------------------------------------------------

该特性允许SF1搜索时自动选取顶级标签，该特性对于
产品搜索十分有用。

例如，在关键词为“iphone”的搜索中，假设顶级标签为
“数码>手机通讯>手机”，那么将仅返回属于标签
“数码>手机通讯>手机”的文档。


激活该特性

在search的Request中，对于需要使用该特性的属性，只
需要在group_label中将该属性相关的value置空即可，例如：

::
    
    {
        "collection": "b5mp",
        "search": {
            "keywords": "iphone",
            "group_label": [
              { "property": "Category",
                "value": [] }
            ]
        }
    }

 
默认情况下，只自动选取一级顶级标签，用户如果需要选取多级，
则需要使用auto_select_limit指定：

::
    
    {
        "collection": "b5mp",
        "search": {
            "keywords": "iphone",
            "group_label": [
                { "property": "Category",
                  "value": [],        
                  "auto_select_limit": 3 
                }
            ]
        }
    }

如果顶级标签存在，Response则返回相应的结果：

::
    
    "top_group_label": [
      { "group_property": "Category",
        "group_label": [
          ["数码", "手机通讯", "手机"],
          ["苹果商城", "iPhone"],
          ["手机", "苹果"]
        ]
       }
     ]

当该顶级标签不存在是，则不返回top_group_label属性。

.. note::
    该特性使用的等级标签可以通过get_freq_group_labels
    查看。如果不存在则可以通过set_top_group_label设置。
    此外，顶级标签也可以通过计算点击频率最高的标签获得。

查询分组-AttrBy Search
=======================

按Attribute分组属于SF1搜索引擎的AttrBy特性，即为将搜索结果按照商品的Attribute
属性进行分组。

商品属性（Attribute）信息
---------------------------

SCD中商品属性字段的内容格式如下：

::
    
    <PropertyName>name:value,name:value...

解释如下：

* PropertyName是字段名称，字段内容是属性名称和属性值的集合
* 每个属性之间用“，”隔开
* 属性名称和值之间用“：”分割
* 如果某个属性名称包含多个属性值，例如某个属性包含3个属性值A，B，C，
  每个属性之间要用“|”隔开。
* 如果属性名称或者属性值中包含“：|”或者双引号字符，那么属性名称
  或者属性值必须用双引号引起来。


下面给出一个SCD的例子：

::
    
    < DOCID >134454555
    < Title >女士风衣
    < Content >时尚女士风衣，大减价
    < Attribute >品牌：Two in one/欧艾尼，领子：圆领，季节：春季|夏季|秋季，年份：2011，尺码:S|M|L|XL

下标给出该scd中的k-v对照：

=============  =================
元素           属性
=============  =================
品牌           Two In One/欧艾尼
领子           圆领
季节           春季|夏季|秋季
年份           2011
尺码           S|M|L|XL
=============  =================

配置文件设置
-------------------

在配置文件中，需要将商品属性字段加入到MiningBundle/Schema/Attr节点中。
如果希望某写属性名称不包含在搜索结果的属性集合中，可以将属性名称加在
Exclude配置中。

.. note::
    Attr字段中最多允许配置一个属性

下面给出一个配置的例子：

::
    
    < DocumentSchema >
        ...
        < Property name/=0Attribute type/=0string / >
    </ DocumentSchema >
    ...
    < MiningBundle >
        < Schema >
            ...
                < Attr >
                < Property name/=0Attribute / >
                < Exclude name/=0ISBN / >
                </ Attr >
        </ Schema >
    </ MiningBundle >


获取Attribute分组结果的API
---------------------------

API的详细介绍请参考
:doc:`../../API/search/API`

下面给出一个JSON请求的例子：

::
    
    {
        " collection " : " example " ,
        " search " : {
            " keywords " : " test "
        },
        " attr " : {
            " attr_result " : true ,
            " attr_top " : 5
        }
     }

其中，各个参数意义如下：

* attr/attr_result：指定是否需要在搜索结果中返回的属性集
* attr/attr_top：默认值为0，0表示在结果集中返回所有的属性集合
  如果大于0，将返回文档数排在最前面的attr_top个属性值结合。

下面给出返回的结果：

::
    
    {
        " attr " : [
            { "document_count " : 10 ,
              "attr_name " : "品牌" ,
              " labels " : [
                { " label " : "阿依莲" ,
                  " document_count " : 7},
                { "label " : "淑女屋" ,
                   "document_count" : 3 },
               ]
            },
            { "documen_count " : 8 ,
              "attr_name " : "尺码",
              " labels " : [
                { " label " : " L " ,
                  " document _count " :5},
                { " label " : " M " ,
                  " document_count " :4},
                { " label " : " XL " ,
                  " document_count " :3},
               ]
            }
         ]
      } 

Select Labels API - 根据搜索返回的Attribute分组信息选择需要的商品Attribute值
-------------------------------------------------------------------------------------
API的详细介绍请参考
:doc:`../../API/search/API`

下面给出JSON请求的例子：

::
    
    {
        " collection " : " example " ,
        " search " : {
            " keywords " : " test " ,
            " attr_label " : [
                { " attr_name " : "品牌" ,
                  " attr_value " : "阿依莲" } ,
                { " attr_name " : "尺码" ,
                  " attr_value " : " L " }
            ]
        },
        " attr " : {
           " attr_result " : true ,
           " attr_top " : 5
        }
    }

