搜索API
===========================

该API提供SF1的核心功能：全文检索，请求(Request)和响应(Response)都以JSON串的
格式包装。

URI: http://server/sf1r/documents/search


参数
-----

请求中各参数说明如下：

    
* collection（字符串类型）：collection名称
* search（对象类型）：提供和搜索相关的参数
   
   * keywords（字符串类型）：用户输入的搜索关键词
   * in（数组类型）：设置搜索在哪儿字段上执行，默认为在所有字段上执行。
   * ranking_model：文档相关度的计算模型。SF1内部有3种模型供用户选择：
       
       * plm模型
       * bm25模型

   * searching_mode：搜索模式。SF1提供了多种常用的搜索模式:

       * and（默认）
       * or
       * wand
       * suffix
       * knn
   * analyzer：关键字分析器的相关参数：

       * use_synonym_extension（布尔类型）：使用使用同义词扩展，默认false。
       * apply_la（布尔类型）：是否对关键字分词，默认true。
       * use_original_keyword（布尔类型）：默认false。

   * query_abbreviation（整数类型）: 指定原始query搜索结果为空时，返回经过查询缩写处理过的搜索结果的个数

* select（数组类型）：设置搜索结果包含哪些字段。
* conditions（数组类型）：搜索结果的过滤条件，需要指定在哪儿属性设置过滤条件。
    
    * property（字符串类型）：属性名称
    * operator（字符串类型）：条件操作符。包含如下：

        * =  ：等于操作符
        * <> ：不等于操作符
        * <  ：小于操作符
        * <= : 小于等于操作符
        * >  : 大于操作符
        * >= : 大于等于操作符
        * between ：区间操作符
        * in : 属于操作符
        * not_in : 不属于操作符
        * starts_with : 前缀操作符，仅适用于字符串属性
        * ends_with : 后缀操作符，仅适用于字符串属性
        * contains : 包含操作符，仅适用于字符串属性
    * value：属性值
* sort（数组类型）：对搜索结果排序
    * property（字符串)：属性名称（按该属性排序）
    * order（字符串）：升序或降序

        * ASC：升序
        * DESC：降序

* limit（整数类型）：限制返回文档的个数，默认20。
* offset（整数类型）：该参数一般和limit一起使用，默认0。如果每页返回10个文档，则第一页
    的参数应该是“limit=10，offset=0”，第二页的参数设置“limit=10，offset=10“
* remove_duplicated_result（布尔类型）：设置搜索结果中是否去除重复文档，默认true，该参数只有当去重服务在<MiningBundle>里配置方才有效。

响应中各参数说明如下：

* total_count（整数类型）：匹配文档总数，如果查询为Suffix模式，则该数值为估计值。
* top_k_count（整数类型）：返回实际的top k结果数目。
* resources（数组类型）：该参数中的每一个对象都是一个文档。每个文档由一系列的K-V对组成，key即为属性名称，而v  alue即为属性值。下面给出可能出现的特殊的属性：

    * DOCID（字符串类型）：
    * _id（整数类型）：SF1内部用来标示文档的ID 
    * _duplicate_document_count（整数类型）：和该文档重复的文档数目，该参数仅当去重计算模块配置时方才有效
    * _similar_document_count（整数类型）：和该文档相似的文档数目，该参数仅当语义相似度计算模块配置时方才有效

* related_queries（数组类型）：字符串类型的数组，标识相关联的查询请求。
* taxonomy（对象类型）：分类结果：

    * labels（数组类型）：分类标签

        * label（字符串类型）：标签名称
        * document_count（整数类型）：属于该标签的文档数
        * sub_labels（数组类型）：子标签数组，结构与本标签一致。

* range（对象类型）：属性值的范围

    * max（浮点数类型）：最大属性值
    * min（浮点数类型）：最小属性值

* count（Map类型）： 返回查询对应某属性的个数，类似Select Count(*) 语义
* refined_query（字符串类型）：在查询结果数目小于设定的阈值时，重新定义的查询请求。
* group（数组类型）：每个对象标识每个属性的分组结果

    * property（字符串类型）：属性名
    * document_count（整数类型）：搜索结果中包含该属性的文档数目
    * labels（数组类型）：分组标签。每个标签表示一个属性值，有如下域：

        * label（字符串类型）：标签名称，标识属性值
        * document_count（整数类型）：该标签下的文档数目
        * sub_labels（数组类型）：子标签数组，与本标签有相同的结构。

* attr（数组类型）：属性数组，每个对象标识一个属性。该数组按照document_count降序排列。

    * attr_name（字符串类型）：属性名称
    * document_count（整数类型）：搜索结果中包含该属性的文档的数目。
    * labels（数组类型）：组标签。每个对象标识一个属性标签，具有如下结构：

        * label（字符串类型）：标签名，标识属性值
        * document_count（整数类型）：搜索结果中包含该标签的文档数目

* top_group_label（数组类型）：每个对象代表一个为属性自动选择的组标签。

    * group_property（字符串类型）：属性名
    * group_label（数组类型）：组标签数组，每个对象代表从root节点到leaf节点的路径。

示例
------

下面给出search的请求与响应示例：

请求：

::

    {
        "collection": "ChnWiki",

        "search": {
            "keywords": "test",
            "log_keywords": true,
            "ranking_model": "plm",
            "analyzer": {
                "use_synonym_extension": true,
                "apply_la": true,
                "use_original_keyword": true
             },
            "in": [
                {"property": "title"},
                {"property": "content"}
            ]
        },

        "group": [
            {"property": "area"},
            {"property": "price"}
        ],

        "attr": {
            "attr_result": true
        }

        // operator list
        // - equal, =
        // - not_equal, <>
        // - greater_than, >
        // - greater_than_or_equal, >=
        // - less_than, <
        // - less_than_or_equal, <=
        // - in
        // - between
        // - starts_with
        // - ends_with
        // - contains
        "conditions": [
            {"property": "id", "operator": "equal", "value": 10},
            {"property": "id", "operator": "in", "value": [10, 20, 30]}
            {"property": "price", "operator": "between", "value": [10.0, 20.0]}
        ],

        "select": [
            { "property": "title" }
            { "property": "content",
            "highlight": false,
            "snippet": true,
            "summary": false,
            "summary_sentence_count": 3,
            "summary_property_alias": "content.summary" }
        ],

        "sort": [
            { "property": "title" },
            { "property": "content", "order": "ASC"},
            { "property": "_rank", "order": "DESC"}
        ],

        "limit": "20",
        "offset": "0",
        "remove_duplicated_result": true,
    }

响应：

::
    
    {
        "header": { "success": true },
        "errors": [
            "Error message 1",
            "Error message 2"
        ],

        "resources": [
            { "title": "xxx",
            "content": "xxx"},
            { "title": "yyy",
            "content": "yyy"}
        ],

        "related_queries": ["a", "b"],

        "taxonomy": {
            "labels": [
            { "label": "animal",
                "document_count": 10,
                "sub_labels": [
                { "label": "dog",
                    "document_count": 6 },
                { "label": "cat",
                    "document_count": 4 }
                ]
            },
            { "label": "music",
                "document_count": 5 }
            ]
        },

        "group": [
            { "document_count": 10,
            "property": "area",
            "labels": [
                { "label": "China",
                "document_count": 7,
                "sub_labels": [
                    { "label": "Shanghai",
                    "document_count": 5 },
                    { "label": "Beijing",
                    "document_count": 2 }
                ]
                },
                { "label": "America",
                "document_count": 3 }
            ]
            },
            { "document_count": 10,
            "property": "price",
            "labels": [
                { "label": "100",
                "document_count": 4 },
                {"label": "200",
                "document_count": 6 }
            ]
            }
        ]

        "attr": [
            { "document_count": 10,
            "attr_name": "品牌",
            "labels": [
                { "label": "阿依莲",
                "document_count": 7},
                { "label": "淑女屋",
                "document_count": 3}
            ]
            },
            { "document_count": 8,
            "attr_name": "尺码",
            "labels": [
                { "label": "L",
                "document_count": 5},
                { "label": "M",
                "document_count": 4},
                { "label": "XL",
                "document_count": 3}
            ]
            }
        ]
    }
