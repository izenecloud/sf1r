商品排序
========

商品排序，是以搜索引擎给出的相关度结果为基础，为了满足商品搜索的特定需求，针对商品分类、商品来源、受欢迎程度等信息，作进一步的排序。

目标
------

商品排序的最终结果，达到的目标如下（优先级从高到低）：

- 通过custom ranking，把特定的商品，排在前面。
- 根据每个商品所属的category计算score，把category score高的商品，排在前面。
- 有比价项的商品，排在前面。
- 没有比价项的商品，通过merchant diversity，使来自不同商家的商品，轮流交替出现；
- 每一轮各商家之间的排序，以merchant score为依据。
- 上述排序方法在判断多个结果的ranking为相同的情况下，按照该商品的受欢迎程度(popularity score)以及搜索引擎给出的相关度(relevance score)为排序依据。

**Custom Ranking**

Custom ranking 是指对搜索结果的排名进行定制，把特定的商品，排在前面。 

首先，我们提供API set_custom_rank，用于设置某个query的搜索结果排名。该API请求的JSON例子如下：

::

	{
	  "collection": "ChnWiki",
	  "resource": {
	    "keywords": "iphone",
	    "top_docid_list": ["111", "222", "333"],
	    "exclude_docid_list": ["444", "555", "666"]
	  }
	}

该API返回结果的JSON例子如下： 

::

	{
	   "resource": {
	     "top_docs": [
	       { "DOCID": "111", "Title": "iphone4S 手机"},
	       { "DOCID": "222", "Title": "iphone4 手机"},
	       { "DOCID": "333", "Title": "iphone3 手机"}
	     ],
	     "exclude_docs": [
	       { "DOCID": "444", "Title": "iphone4S 配件"},
	       { "DOCID": "555", "Title": "iphone4 配件"},
	       { "DOCID": "666", "Title": "iphone3 配件"}
	     ]
	}

最后，我们还提供了API get_custom_query，用于得到所有被定制排名的query。该API请求的JSON例子如下：

::

	{
	  "collection": "ChnWiki"
	}

该API返回结果的JSON例子如下

::

	{
	  "resources": ["iphone", "ipad", "imac"]
	}

**Category Score**

Category score是指给定query，得到对应的top labels。每个top label对应一个category score，排名在前的label，分数越高。
对于每个doc，如果它的category属于其中某个top label，则得到对应的category score。

top labels可以通过下面几种方法得到（优先级从高到低）:
- API search的参数boost_group_label指定本次搜索对应的top labels。
- API set_top_group_label 设置某个query对应的top labels。
- API log_group_label用于记录每个分类的点击次数，点击次数最多的成为top labels。
- 通过自动分类的方法，找到query最有可能对应的top label。

下面是调用 API log_group_label请求的JSON例子。当用户点击某个分类时，需要调用该API记录点击次数。

::

	{
	  "collection": "b5mp",
	  "resource": {
	    "keywords": "iphone",
	    "group_property": "Category",
	    "group_label": ["数码", "手机通讯", "手机"]
	  }
	}

**Merchant Diversity**

Merchant diversity 是为了保证商家来源多样性的一种方法。 

在搜索结果里,为了让用户看到多种商家来源，有更多选择不同商家的机会，merchant diversity方法会在上述排序结果的基础上，使来自不同商家的商品，交替出现。 

注：该方法只影响没有比价项的商品，对那些有比价项的商品，由于已经对应多个商家，其排名将不受影响，保持不变。

**Merchant Score**

在上述merchant diversity的每一轮中，各商家之间的排序，以merchant score为依据。

首先，我们提供API set_merchant_score，用于设置商家的总体分数，同时也可以设置该商家在某个分类下的分数。
在排序时，假设该商品属于分类C，如果该商家在分类C下有分数，则使用该分数作为merchant score。
否则，使用该商家的总体分数作为merchant score。如果没有设置过，默认merchant score为0。

API set_merchant_score请求的JSON例子如下：

::


	{
	  "collection": "ChnWiki",
	  "resource": [
	    { "merchant": "京东",
	      "score": 8,
	      "category_score": [
		{ "category": ["手机数码", "手机通讯", "手机"], "score": 9.2 },
		{ "category": ["服饰鞋帽", "男装"], "score": 8.5 }
	      ]
	    }
	  ]
	}

**Popularity Score**

Popularity score反映了商品的受欢迎程度。给定某个商品，把它对应的一些属性值作加权平均计算，就得到了popularity score。 

这些属性可以在下面的配置里指定，比如点击次数，分享次数，评论分数等等。由于这些属性值经常变化，系统会定期更新所有商品的popularity score。

**Random Ranking**

除了上述的merchant diversity之外，在某些场景下，为了给用户提供新奇感，我们还提供了一种随机排序。 

为了启用随机排序，除了需要在下面的配置里指定以外，还需要把API search的参数is_random_rank指定为true。 

注：每个query随机排序的结果，系统会在cache里保留1小时。1小时后，系统会返回另一种随机排序的结果

SF1 配置
--------------

下面是b5mp.xml里配置ProductRanking的例子：

::

  <MiningBundle>
    <Schema>
      <Group>
        <Property name="TargetCategory" />
        <Property name="Source" />
      </Group>

      <ProductRanking>
        <Score type="custom" weight="100" />
        <Score type="category" property="TargetCategory" weight="10" />
        <Score type="offer_item_count" property="itemcount" />
        <Score type="diversity" property="Source" />
        <Score type="merchant" property="Source" />
        <Score type="popularity" weight="1">
          <Score property="itemcount" weight="1" />
          <Score property="Score" weight="0.8" />
          <Score property="Fenxiang" weight="0.5" />
          <Score property="Guanzhu" weight="0.5" />
          <Score property="Xihuan" weight="0.5" />
          <Score property="BuyCount" weight="0.5" />
          <Score property="_ctr" weight="0.5" />
          <Score property="DATE" weight="0.2" />
        </Score>
        <Score type="random" weight="1" />
        <Score type="relevance" weight="0.001" />
        <Score type="fuzzy" weight="0.1" />
      </ProductRanking>

    </Schema>
  </MiningBundle>

ProductRanking的每个配置项都是可选的，可以根据实际需要进行配置，各配置项含义如下：

- Score type="custom"里的weight，指定custom ranking的权重，由于比其它配置项的权重高，custom ranking的优先级是最高的。
- Score type="category"里的property，指定category score的property，必须来自配置项Group。
- Score type="offer_item_count"里的property，指定比价项个数的property，必须是数值类型。
- Score type="diversity"里的property，指定merchant diversity的property，必须来自配置项Group。
- Score type="merchant"里的property，指定merchant score的property，同样必须来自配置项Group。
- Score type="popularity"里的weight，指定popularity score的权重。由于popularity score是多个属性值的加权平均，每一个属性值在popularity的子选项中配置。这些属性必须是数值类型、DATE或者_ctr(点击次数)，每一个属性都可以配置各自的weight作为权重。
- Score type="random"里的weight，指定任意非零值，即代表启用random ranking。
- Score type="relevance"里的weight，指定relevance score的权重。
- Score type="fuzzy"里的weight，指定fuzzy score的权重。
