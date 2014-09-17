推荐引擎API
===================================

FBT(Frequently Bought Together)
-----------------------------------

根据购买的商品推荐其他经常和这商品一起购买的商品。该API是对订单数据进行挖掘分析。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-FBT。

  --max_count(uint)：返回的最大个数，默认为10。

  --input_items(array)：输入的商品信息。

    --ITEMID(string)：商品id，等同商品信息SCD中的DOCID

- 输出参数：

  resources(array)：返回的一起购买的商品。

- JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"FBT",
      "max_count":20,
      "input_items":
      [
        {
          "ITEMID":"408"
        }
      ]
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":0.9,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":0.8,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

BAB(Bought Also Bought)
-------------------------

买了某些商品的人还买了其他的商品。该API是对订单数据进行挖掘分析。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-BAB。

  --max_count(uint)：返回的最大个数，默认为10。

  --input_items(array)：输入的商品信息。

    --ITEMID(string)：商品id，等同商品信息SCD中的DOCID

- 输出参数：

  resources(array)：返回的一起购买的商品。

- JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"BAB",
      "max_count":20,
      "input_items":
      [
        {
          "ITEMID":"408"
        }
      ]
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":0.9,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":0.8,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

VAV(Viewed Also View)
-------------------------

看过某些商品的人还看过其他的商品。该API是对浏览数据进行挖掘分析，依赖于visit_item(浏览商品) API。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-VAV。

  --max_count(uint)：返回的最大个数，默认为10。

  --input_items(array)：输入的商品信息。

    --ITEMID(string)：商品id，等同商品信息SCD中的DOCID

- 输出参数：

  resources(array)：返回的一起看过的商品。

- JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"VAV",
      "max_count":20,
      "input_items":
      [
        {
          "ITEMID":"408"
        }
      ]
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":0.9,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":0.8,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

BOE(Based on Event)
--------------------

此推荐必须在用户已登录的情况下使用。此推荐API需要依赖于以下API：
1）purchase_item(购买商品)
2）update_shopping_cart(更新购物车)
3）track_event（用户行为跟踪）
4）rate_item（评星级）

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-BOE。

  --max_count(uint)：返回的最大个数，默认为10。

  --USERID(string)：用户ID。


- 输出参数：

  resources(array)：返回基于用户行为推荐的商品。

- JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"BOE",
      "max_count":20,
      "USERID":"5fa9c3e643e3e993bab4eb8f9b465c42"
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":0.9,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":0.8,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

BOB(Based on Browse History)
------------------------------

基于用户浏览历史的推荐。

此推荐分两种模式，分别是用户已登录和用户未登录：

1.未登录用户：需要提供input_items参数，不需要提供USERID和session_id参数。
2.登录用户：需要提供USERID和session_id参数，不需要提供input_items参数。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-BOB。

  --max_count(uint)：返回的最大个数，默认为10。

  --USERID(string)：用户id，只在用户已登录的情况下使用此字段。

  --session_id(string)：session id，从用户登录到登出的唯一标识，且在此期间不能改变。

  --input_items(array)：浏览的商品信息，用户未登录的情况下使用此字段作为该匿名用户的浏览历史。

    --ITEMID(string)：商品id，等同商品信息SCD中的DOCID

- 输出参数：

  resources(array)：返回基于浏览历史推荐的商品。

- 用户已登录的JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"BOB",
      "max_count":20,
      "USERID":"5fa9c3e643e3e993bab4eb8f9b465c42",
      "session_id":"5fa9c3e643e3e993bab4eb8f9b465c42",
      "input_items":
      [
        {
          "ITEMID":"408"
        }
      ]
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":0.9,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":0.8,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

BOS(Based on Shopping Cart)
------------------------------

基于购物车的推荐。

此推荐分两种模式，分别是用户已登录和用户未登录：

1.未登录用户：需要提供input_items参数，不需要提供USERID参数。
2.登录用户：需要提供USERID参数，不需要提供input_items参数。依赖于update_shopping_cart(更新购物车)API。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-BOS。

  --max_count(uint)：返回的最大个数，默认为10。

  --USERID(string)：用户id，只在用户已登录的情况下使用此字段。

  --input_items(array)：商品信息，用户未登录的情况下需提供购物车中的商品信息。

    --ITEMID(string)：商品id，等同商品信息SCD中的DOCID

- 输出参数：

  resources(array)：返回基于购物车推荐的商品。

- 用户已登录的JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"BOS",
      "max_count":20,
      "USERID":"5fa9c3e643e3e993bab4eb8f9b465c42",
      "input_items":
      [
        {
          "ITEMID":"408"
        }
      ]
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":0.9,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":0.8,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

BOR(Based on Random)
-------------------------

随机的推荐。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-BOR。

  --max_count(uint)：返回的最大个数，默认为10。

  --USERID(string)：用户id，使用此参数后返回结果集中不包括该用户purchase，visit，shopping_cart，track事件请求过的商品信息。

  --input_items(array)：商品信息，使用此参数后返回结果集中不包括该参数中的ITEMID商品信息。

    --ITEMID(string)：商品id，等同商品信息SCD中的DOCID

- 输出参数：

  resources(array)：返回随机的商品。

- 用户已登录的JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"BOR",
      "max_count":20,
      "USERID":"5fa9c3e643e3e993bab4eb8f9b465c42",
      "input_items":
      [
        {
          "ITEMID":"408"
        }
      ]
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":0.9,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":0.8,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

BAQ(Bought after Query)
-------------------------

该API对用户搜索关键词后最终购买商品进行统计。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --rec_type(string)：推荐类型-BAQ。

  --max_count(uint)：返回的最大个数，默认为10。

  --keywords(string)：搜索关键词。

- 输出参数：

  resources(array)：返回用户搜索关键词后最终购买的商品。

- JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "rec_type":"BAQ",
      "max_count":20,
      "keywords":"江浙菜"
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "total_freq":50,
    "resource":
    [
      {
        "ITEMID":"1",
        "weight":30,
        "name":"iphone",
        "link":"www.shop.com/product/item_001",
        "price":"5000",
        "category":"digital"
      },
      {
        "ITEMID":"306",
        "weight":20,
        "name":"ipad",
        "link":"www.shop.com/product/item_002",
        "price":"6000",
        "category":"digital"
      }
    ]
  }

TIB(Top Item Bundle)
-------------------------

该API对高于一定频率的商品进行统计。

- 输入参数：

  collection(string)：collection名字。

  resource(object)：请求。

  --max_count(uint)：返回的最大个数，默认为10。

  --min_freq(uint):返回频率的最小值。

  --select(array)：返回结果的属性字段选择。

- 输出参数：

  resources(array)：返回高于一定频率的商品集合。

- JSON请求的例子：

::

  {
    "collection":"example",
    "resource":
    {
      "max_count":20,
      "min_freq":10,
      "select":
      [
        "Title"
      ]
    }
  }

- JSON返回的结果：

::

  {
    "header":
    {
      "success":true
    },
    "resource":
    [
      {
        "freq":90,
        "items":
        [
          {
            "Title":"iphone"
          },
          {
            "Title":"ipad"
          }
        ]
      },
      {
        "freq":85,
        "items":
        [
          {
            "Title":"ipad"
          },
          {
            "Title":"imac"
          }
        ]
      }
    ]
  }
