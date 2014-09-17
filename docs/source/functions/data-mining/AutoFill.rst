自动补全
========


功能介绍
----------------



建立和更新
-----------------

建立
~~~~~~~~~~~~

一般来说，自动填充来自quereis的。建立之前，我们将删除那些查询中标点，如“（iphone |ipad）”或“（iphone! IPAD）”。此外，查询也将被被标准化处理，包括字母大小写转换，以及空格的处理。

在那之后，所有的查询可以划分成3种查询。全中文查询，全拼音（英文查询），中文拼音混合查询。为了建立自动补全的，每个查询必须被分割成如下短语，如：
如果查询是全英文的，如：“iPhone4S”。
下面是分割的实例：

它将会被分割成：

::

         iphone 4s
         iphone 4
         iphone
         iphone
         iphon
         ipho
         iph
         ip
         i

一共 9 个前缀;
如果查询是全中文的，入:"三星".
它将会被分割成：

::

         sanxing
         sanxin
         sanxi
         sanx
         san
         sa
         s
         san星
         三xing
         三xin
         三xi
         三x
         三
         三星

一共 14个前缀;
Here, "iphone 4s" is a value,

三星galaxy ," 是混合型的, 它将会被分割成：:

::

         三星 galaxy,
         sanxing galaxy,
         sanxing galax
         sanxing gal,
         sanxing ga
         sanxing g ,
         sanxing
         sanxing ,
         sanxin
         sanxi,
         sanx
         san ,
         sa
         s,
         san星 galaxy
         san星 galax ,
         san星 gala
         san星 gal ,
         san星 ga
         san星 g ,
         san星
         san星 ,
         三xing galaxy
         三xing galax ,
         三xing gala
         三xing gal , 三xing ga
         三xing g ,
         三xing
         三xing ,
         三xin
         三xi ,
         三x
         三

一共 36个前缀;
所有的前缀和查询将会被用来建立key-value索引,一个key（前缀）后面是以其为前缀的查询  eg:

::

         Key:s > Value:三星galaxy, 三星
         Key:sa > Value:三星galaxy, 三星
         Key:san 3> Value:三星galaxy, 三星
         ......


如果一个前缀关键字有很多值, 所有值将会按照查询搜索频率进行排序, 以保证最流行的关键字出现在靠前的结果中.

存放
~~~~~~~~~~~~

由于所有的k-v的总空间成本将是非常大的，所以
我们把所有的数据放到磁盘上，然而，如果我们总是从磁盘中查找，这将会
非常慢。所以，考虑到我们只需要每个关键字对应的前N个值，我们
每个键的前N值将存储在内存以便快速访问;


使用自动补全
~~~~~~~~~~~~~~~~~
一旦自动补全已经建立，为每个输入（比如：“i”或“ip”或“iph”...）
其在倒排索引中其实是一个关键字，自动补全将返回其前N值，按照频率排序。如果输入的是不是一个关键值，它将
返回值将为空。


数据结构
----------------

leveldb
~~~~~~~~~~~~
LevelDB是一个轻量集的K-V数据库，提供了关键字字符串键到值字符串的映射。在这里，我们使用leveldb
在磁盘上的存储（只在建立和更新中使用）。
了解更多：http://code.google.com/p/leveldb/

小波树
~~~~~~~~~~~~
小波树是一个简洁的数据结构来存储压缩字符串
空间。在这里，我们使用小波树来存储前对应Key的按照频率排序的前N个值。
因为多个不同的Key可以对应相同的value，用小波树可以大大的节省空间。



操作和更新
----------------

初始化和重新启动
~~~~~~~~~~~~~~~~~~~~~~~
1.初始化。假设一个collection中的AutoFill还没有建立，当该collection start之后，这个collection 的
AutoFill将会自动建立。事实上，对于大量的
查询，如果使用过去90天建立，它将会花费一些时间。对于AutoFill
更新，现在需要只需要数秒种。
2.重新启动。如果要检查该collection里是否已然建立AutoFill，我们将检查
三个部分：itemlist，wavelet tree, 以及itemdb字典。一旦
AutoFill已经存在，对于百万级的关键字，重新启动只需要数秒种。

更新
~~~~~~~~~~~~~~~~~~~~~~~
每一天都会有新的查询。因此，自动完成将设置一个cron job来进行自动更新，更新的数据包括昨天的查询。更新时，新的查询将被添加
进入自动填充，并且现有查询的频率和命中数也将被更新。

