基础排序
==========

对检索文档进行排序，就是首先根据检索词和文档的相关度、文档自身权重值、以及文档预算设定的权重值等众多因素计算获得文档在本次检索中权重值，然后根据权重进行获取topK检索结果的过程。

TF IDF算法
------------

TF和IDF都是计算检索词和文档相关性的一个要素； 

- 词频 (term frequency, TF) 指的是某一个给定的词语在该文档中出现的次数； 

- 逆向文件频率 (inverse document frequency, IDF) 是一个词语普遍重要性的度量。某一特定词语的IDF，可以由总文件数目除以包含该词语之文件的数目，再将得到的商取对数得到。  

一般来说，单词出现的次数越多，即tf越大，则这个词越能表达页面的意思;
而出现在越少页面中的词，即idf越小的词越能表达页面的意思.

该算法一般直接用tf/idf来表示最终的权重.

BM25算法
---------

BM25算法，通常用来作搜索相关性平分。其主要思想：对查询Query进行语素解析，生成语素qi；然后，对于每个搜索结果D，计算每个语素qi与D的相关性得分，最后，将qi相对于D的相关性得分进行加权求和，从而得到Query与D的相关性得分。

在BM25中，通过对于系数的调试，BM25能够给出比tf/idf更好的相关性结果；其使用方法是在search请求中添加：

::

	"ranking_model":"bm25"

PLM
------

The proximity of query terms in a document is a very important information to enable ranking models go beyond the "bag of word" assumption in information retrieval. This paper studies the integration of term proximity information into the unigram language modeling. A new proximity language model (PLM) is proposed which views query terms' proximity centrality as the Dirichlet hyper-parameter that weights the parameters of the unigram document language model. Several forms of proximity measure are developed to be used in PLM which could compute a query term's proximate centrality in a specific document. In experiments, the proximity language model is compared with the basic language model and previous works that combine the proximity information with language model using linear score combination.

- 该方法可以获得较好的排序精度，但是其排序效率较Bm25较低。其使用方法是在search请求中添加：

::

	"ranking_model":"plm"

