查询意图
=========

该功能用于从用户输入的查询关键词中提取用户的查询意图，为用户提供更精确的搜索结果。

配置文件
----------

如果需要sf1针对某个属性对用户输入的关键词进行意图预测，则需要在MiningBundle的QueryIntent
部分配置该属性，示例如下：

::

     <MiningBundle>
        <Schema>
          ...
          <QueryIntent>
            <Property name="TargetCategory" type="trie" operator="starts_with" operands="1"/>
            <Property name="Source" type="lexicon" operator="in" operands="-1"/>
          </QueryIntent>
        </Schema>
      </MiningBundle>

配置文件中各参数解释如下：

========     ============        =======================================================
参数名       意义                备注
========     ============        =======================================================
name         属性名称            无
type         预测器类型          目前，预测效果较好的有两种：trie与lexicon
operator     操作符              即所有sf1的condition支持的操作符
operands     操作数的数目        该参数为正整数，若为-1则表示任意数目的操作数。
========     ============        =======================================================

资源文件格式
------------

预测器trie与lexicon都是基于词典的精确匹配，结构如下：

trie词典格式:

::
    
    个护化妆
    [
        口腔护理
        [
            牙刷/牙线
            牙膏/牙粉
            牙齿美白仪
        ]
    ]

同时，可以为trie词典指定同义词，文件格式如下：

::
    
    保护套 保护壳:手机保护套 笔记本保护套
    充电器:手机充电器

其中，"保护套 保护壳:手机保护套"表示“保护套”与“保护壳”的同义词为“手机保护壳”。这样，如果
用户关键词中出现“保护套”或“保护壳”，则映射到“手机保护套”或者“笔记本保护套”

lexicon词典格式:

::
    
    卓越亚马逊:卓越;亚马逊;amazon
    天猫:天猫商城
    文轩网官网:文轩;文轩网

其中，“：”之前为sf1内部使用的标准名称，之后即为该名称对应的同义词。

资源文件命名
--------------

预测各个属性所需的资源文件存储在sf1的resource文件夹下的query-intent路径下，文件名与该属性相同，
例如，需要对“TargetCategory”属性做查询意图，则该资源文件命名为“TargetCategory”；另外，如果需要
为该属性指定同义词，则命名为“属性”+“Synonym”，对“TargeCategory”即为“TaregetCategorySynonym”。

资源文件更新
-------------------

配置文件示例如下：

::
    
    nginx:
        ip: localhost
        port: 8888

    mode:  

    collection:
        - b5mp

    directory: ../../../package/resource/query-intent

    lexicon:
        - Source
    trie:
        - TargetCategory

其中，需要说明的参数如下：

===========     ================================
参数            说明
===========     ================================
mode            该脚本支持cron模式与非cron模式.
                cron模式：每天凌晨两点自动更新;
                非cron模式：立即更新并退出.
collection      指定需要更新资源文件的collection.
lexicon         指定资源文件为lexicon格式的属性.
trie            指定资源文件为trie格式的属性.
===========     ================================

.. note::
    对于lexicon类型的资源文件，可以手动修改

用法：

::
    
    ./cron.rb

查询意图API
------------

默认情况下，该组件将对所有配置文件中的属性做意图预测操作，
用户也可以通过API指定某一个或几个属性，示例如下：

::
    
    {
      "collection" : "b5mp",
      "search" : {
        "keywords" : "京东"
      },
      "query_intent": [ 
        {"property" : "Source"}
      ],
      .....
    }

该示例指定查询意图组件只对“Source”属性做意图预测。
