商品collection配置说明
========================

字段约定
-------------------
商品的3个集合b5mo,b5mp,b5mc的字段配置目前达成如下约定


* 类目字段

OriginalCategory代表抓取商家数据的原始类目；Category代表B5M的后端类目，该类目从淘宝的后端类目裁剪而来，
保持稳定；TargetCategory代表B5M的前端类目，网站的搜索都基于该类目显示。TargetCategory目前暂时保持稳定，
因为CPC的竞价机制临时采用对类目竞价的手段进行，在未来，TargetCategory可能会根据时间，季节做出调整以跟进
业务的变化。


* 比价字段

b5mp的itemcount字段代表该Product聚合了多少个Offer，如果该字段为1，表明只聚合了一个Offer。
mobile字段来自此前应用的约定，该字段为1时代表可以根据某固定规则从Web的URL转换成为WAP的URL。
uuid是最重要的字段，b5mo的uuid就是对应b5mp的DOCID，因此uuid可以看作传统RDBMS的外键关联字段。

* 商家字段

Source代表商家字段信息。b5mo的Source只可能有一个，因为某特定Offer只可能来自某一个商家；而b5mp的
Source则是由逗号分隔的一系列商家名，因为b5mp需要包含聚合后的信息。例如，"iphone 4s手机"如果有3个商家，
那么b5mp对应的Source字段可能包含如下内容: "当当网,淘宝网,亚马逊"。

* 价格字段

b5mp的价格字段是一个区间值，包含了所有对应Offer的价格区间。

* 搜索字段

目前，对于普通倒排索引模式的查询（系统默认），配置了Title，TargetCategory，Source三个字段进行查询，而
对于Suffix查询模式，只配置了Title字段，这是因为TargetCategory和Source对于相关性的查找并无帮助，对该类
字段的查找用DB Style查询更加适合，在以后的Query Classifier中，SF1会自动对查询进行如上处理。

目前，分组显示配置了TargetCategory，Source，Brand三个字段。


