查询
====

这部分对SF1支持的查询模式进行简单的介绍，相应的API接口参看
:doc:`../../API/search/API`

布尔查询
--------

布尔查询基于倒排索引：给定查询，SF1首先会针对该查询进行分词处理，在AND查询方式下，所有分词之间
是逻辑"与"关系，意味着返回包含所有分词结果的文档；而OR查询方式下，分词之间是逻辑“或”关系，意味着
只要有一个词命中，该文档既可能返回。


SF1支持通用的布尔查询方式（AND，OR）.AND相对于OR，有更快的查询速度和更好的查询结果，
OR则能够返回更多的查询结果。因此，默认情况下采用AND查询。

下面给出使用AND模式的实例：

::
    
    {
        "collection":"b5mp",
        "search":
        {
            "keywords":"she star",
            "searching_mode":
            {
                "mode":"and"
            }
        }
    }

.. note::
    SF1缺省的查询方式为AND，如下请求使用AND模式查询
    
    ::
    
        {
            "collection":"b5mp",
            "search":
            {
                "keywords":"你好 世界",
            }
        }

        

WAND查询
--------


AND或OR的查询方式，由于其定义，主要缺陷在于完全匹配会导致太多或者太少的结果文档被返回。
WAND是Weak（Weighted） AND的缩写，是一种新型的布尔查询方式，同样基于倒排索引，通过对
每个查询关键赋予一定权重的方式，以期弥补AND或OR查询的缺陷。可以通过权重以及参数的调整，
使WAND查询返回合适的结果，而AND与OR查询则是WAND查询的上界与下界。其查询效率和查询质量
介于AND 与 OR之间。


下面给出使用WAND模式查询的实例：

::
    
    {
        "collection":"b5mp",
        "search":
        {
            "keywords":"she star",
            "searching_mode":
            {
                "mode":"wand"
                "threshold":"0.35"
            }
        }
     }

.. note::
    WAND查询可以指定threshold属性。threshold越接近1，WAND表现得越像AND

Suffix查询
------------

Suffix查询完全不同于以上查询，因为前者基于倒排索引进行，而Suffix查询基于最新型的
简洁数据结构(Succinct Index)，其基本原理是，对文档数据构建压缩后缀数组，因此在索引
的时候是不存在分词的，这意味着可以针对任意子串进行查询。


对于使用Fuzzy查询时各个参数意义解释如下：

* lucky（Uint类型）：指定该次检索的top k的值。这里之所以命名为lucky，是因为出于
  效率的考虑，此处并不是完全精确的top k，而是有一定程度的近似。
* use_fuzzy（Bool类型）：当指定该值为true时，将进行类似上述的WAND查询，所不同的是，
  该查询是在后缀数组上进行，而非倒排表结构。 返回结果是对查询里所有分词结果取并的Top K，K
  的大小既为lucky； 当指定该值为false时，搜索结果仅仅返回本次查询的最长后缀
  (The search result only contain the longest suffix of query)。

下面给出使用该模式的Request例子：

::
    
    {
        "collection":"b5mp",
        "search":
         {
            "keywords":"SHE奇幻旅程",
            "searching_mode":
            {
                "mode":"suffix",
                "lucky":"100",
                "use_fuzzy":true
            }
        }
    }

相应Response请求如下：

::
    
    {
        "header":
        {
            "success":true
        },
        "resources":
        [
            {
            "Attribute":"",
            "Brand":"",
            "BuyCount":"",
            "Category":"音乐/影视/明星/音像>音乐CD/DVD"
            "Combined":"",
            "CommentCount":"",
            "Content":"",
            "DATE":"20130121T004157",
            "DOCID":"d7dadb40f63c2ecde6e2959db753c54e",
            "Fenxiang":"",
            "Guanzhu":"",
            "OldOfferIds":"",
            "OriginalCategory":"歌曲>其他流行音乐",
            "OriginalPicture":"",
            "PicPrpt":"",
            "Picture":"http://img.b5m.com/image/T1WOhTBQEg1RCvBVdK/",
            "Price":"16.00",
            "Score":"",
            "Source":"230",
            "SubSource":"",
            "TargetCategory":"图书音像>音像>音乐",
            "Title":"SHE奇幻旅程",
            "Url":"http://detail.bookuu.com/0607145.html",
            "Xihuan":"",
            "_id":4631779,
            "_rank":10.45418739318848,
            "itemcount":"1",
            "mobile":""
        },
        ...
        {
            "Attribute":"",
            "Brand":"",
            "BuyCount":"",
            "Category":"钟表首饰>发饰",
            "Combined":"",
            "CommentCount":"",
            "Content":"",
            "DATE":"20130202T074337",
            "DOCID":"7081fba55cf6b50a507044d0322f08fc",
            "Fenxiang":"",
            "Guanzhu":"",
            "OldOfferIds":"",
            "OriginalCategory":"饰品/流行首饰/时尚饰品新>发饰",
            "OriginalPicture":"",
            "PicPrpt":"",
            "Picture":"http://img.b5m.com/image/T1rMVTBm_T1RCvBVdK/",
            "Price":"136.00",
            "Score":"",
            "Source":"403",
            "SubSource":"shes饰品旗舰店@天猫商城",
            "TargetCategory":"珠宝饰品>时尚饰品>头饰",
            "Title":"she's茜子正品 茜子头箍 浪漫田园印花手工布艺包边带齿发箍发卡",
            "Url":"http://detail.tmall.com/item.htm?id=19695804534",
            "Xihuan":"",
            "_id":6836768,
            "_rank":0.4242057502269745,
            "itemcount":"1",
            "mobile":""
        }
       ],
    "top_k_count":100,
    "total_count":18154
    }

为了说明use_fuzzy字段的作用，下面给出省略use_fuzzy字段的插叙的response：

::
    

    {
        "header":
        {
            "success":true
        },
        "resources":
        [
            {
            "Attribute":"",
            "Brand":"",
            "BuyCount":"",
            "Category":"音乐/影视/明星/音像>音乐CD/DVD"
            "Combined":"",
            "CommentCount":"",
            "Content":"",
            "DATE":"20130121T004157",
            "DOCID":"d7dadb40f63c2ecde6e2959db753c54e",
            "Fenxiang":"",
            "Guanzhu":"",
            "OldOfferIds":"",
            "OriginalCategory":"歌曲>其他流行音乐",
            "OriginalPicture":"",
            "PicPrpt":"",
            "Picture":"http://img.b5m.com/image/T1WOhTBQEg1RCvBVdK/",
            "Price":"16.00",
            "Score":"",
            "Source":"230",
            "SubSource":"",
            "TargetCategory":"图书音像>音像>音乐",
            "Title":"SHE奇幻旅程",
            "Url":"http://detail.bookuu.com/0607145.html",
            "Xihuan":"",
            "_id":4631779,
            "_rank":10.45418739318848,
            "itemcount":"1",
            "mobile":""
        },
      "top_k_count":1,
      "total_count":2
    }

该Response与默认搜索模式的Response一致。

.. note::
    何时使用布尔查询，WAND查询，Suffix查询，取决于业务需求。 一般情况下，基于倒排表的查询（包括布尔查询，WAND查询）适合需要精确分页，性能要求不很高的场景，而Suffix查询适合于极高并发场景（在默认情况下，Suffix查询性能是布尔查询的十倍以上）。然而，Suffix查询存在一定程度的近似，并且只适合于针对相关度的查询。换句话，如果期望根据多字段排序，并且需要输出所有的结果，那么就不适合采用Suffix查询，而且即便采用了，那么也只能在返回结果的Top K里边进行要求的排序，而无法在所有结果里排序。



数据库风格查询
----------------
当查询Request的"keywords"字段为"*"时，该查询既为数据库风格查询。


对于该类查询模式，一般需要指定查询条件，否则查询结果将毫无意义。
查询条件的格式以及参数意义请参看
:doc:`../../API/search/API` 中对condition参数的介绍。

下面给出使用该类查询模式的Request实例：

::

    {
        "collection":"b5mp",
        "search":
        {
            "keywords":"*"
        },
        "conditions":
        [
         {
           "property":"Price",
           "operator":">",
           "value":
           [
            100
           ]
        }
       ]
    }
 
对于该请求，将返回所有Price大于100的文档。
