使用方法和配置
===============

SF1的分词库（ilplib）支持对包括中，日，韩，英文在内的多语言进行分词。
对于中文分词来说，提供了基于词表和统计模型(ME)的方法, 基于词表的方法有正向最大匹配和最小匹配等，
通常基于统计模型的分词方法准确率更高，而基于词表的方法性能更快。
SF1中出于性能的考虑，主要使用基于词表的方法，另一方面，检索结果的正确率和查全率，也与检索和排序的策略相关。

1. 使用方法
-------------

SF1对分词的使用主要包括索引和查询两个方面: 

一方面，对文档进行索引时，需要先将文档切分成索引词；
另一方面，对于用户的查询，也需要先切分成词才能在索引中进行查询。 

原则上，索引和查询所使用的分词方法应该保持一致。 

分词方法的选择，主要是通过在配置文件中进行相关的设置，详细的配置请参考2.2节。

下面结合配置文件，以中文分词为主，介绍分词的基本使用方法。

2. 配置
---------


2.1 基本分词方法
~~~~~~~~~~~~~~~~~

基本分词方法的配置如下：

::

	<Method id="inner_la_cnall_sia" analysis="chinese" casesensitive="no" >
		<settings mode="label" option="R+S-V-T3" specialchar="#" dictionarypath="/home/zhongxia/codebase/icma/db/icwb/utf8"/>
	</Method>
	<Method id="la_sia" analysis="multilang" advoption="default,inner_la_korall_sia;en,inner_la_cnall_sa;cn,inner_la_cnall_sia"/> 

id 是唯一标识分词方法的名称, 上面的配置定义了一个多语言的分词方法 "la_sia", 它内部使用的中文分词方法为"inner_la_cnall_sia"。

其中，需要注意的属性是“Method/settings/option”：

- option="R+S-V-T3"，代表分词设置为正向最小匹配，不包含unigram分词结果，但可能含overlap的情形(例如，“中国人”的分词结果为，“中”，"中国"，“国”，“国人”，“人”)。是通常情况下的分词配置。 

- option="R+S-V-T4", 代表分词设置为正向最小匹配，且包含unigram分词结果，即，不管有没有overlap, unigram结果都会输出。这种分词输出的词比较多，对性能有一定影响，而且需要确保索引中含有unigram作为索引词，目前少用。 

- option="R+S-V-T5"，代表分词设置为正向最小匹配，不包含unigram分词结果，也不含overlap情形。在用户词典中的词已经比较丰富全面的情况下，可使用此配置作为搜索（查询）的分词方法。

::

	<Method id="la_unigram" analysis="char" advoption="part" casesensitive="no" /> 

在unigram分词方法中，advoption="part"，代表数字和字母将作为整体，而不按字符进行切分；如果设置为advoption="all"，则代表数字和字母也会按字符进行切分。


**索引的分词配置**

在Collection的配置文件中，将属性 "IndexBundle/Schema/Property/Indexing/analyzer" 设置为相应的分词id，即可指定索引时按指定的分词方法进行切分。

**查询的分词配置**

解析用户查询时所用的分词方法，通常与对应Property索引用的分词方法一致。
原则上，应使索引分词方法的结果都包含于查询分词方法的结果，以保证查全率。当前，查询分词无需提供另外配置。


2.2 详细配置策略
~~~~~~~~~~~~~~~~~

SF-1 R 中有多种语言分析方法，其中一些是基于字符的分析方法，还有一些是基于语言的分析方法。标记解析器的解析结果作为分析器的输入，分析器对这些字符进行分析，得到一系列的字符。最终我们对这些字符再进行索引，搜索和挖掘。

下面是关于语言分析器的一些配置信息：

::

	<LanguageAnalyzer dictionarypath="path_of_knowledge" >
	  <Method id="la_token" analysis="token"  />
	  <Method id="la_char" analysis="char"  />
	  <Method id="la_unigram" analysis="char"  />
	  <Method id="la_ngram" analysis="ngram"  apart="n" schflag="second"
		  max="3" min="2" idxflag="second" maxno="256" />
	  <Method id="la_bigram" analysis="ngram"  apart="n"
		  max="2" min="2" idxflag="second" maxno="2194967296" />
	  <Method id="inner_la_unigram" analysis="ngram" apart="n"
		  max="1" min="1" idxflag="second" maxno="2194967296" />
	  <Method id="la_matrix" analysis="matrix"  prefix="yes" suffix="yes" />
	  <Method id="la_korall" analysis="korean" >
	    <settings mode="all" dictionarypath="" specialchar="#" option="R+S+" />
	  </Method>
	  <Method id="la_eng" analysis="english" >
	    <settings mode="all" dictionarypath="" option="S+" />
	  </Method>
	  <Method id="la_eng_case_sensitive" analysis="english"  casesensitive="yes">
	    <settings mode="all" dictionarypath="" option="S+" />
	  </Method>
	  <Method id="inner_la_korall_mia" analysis="korean"  casesensitive="yes">
	    <settings mode="label" dictionarypath="" specialchar="#" option="R1S-" />
	  </Method>
	  <Method id="la_mia" analysis="multilang"  lower="no"
		  casesensitive="yes" advoption="default,inner_la_korall_mia;cn,la_char" />
	  <Method id="inner_la_korall_sia" analysis="korean"  casesensitive="yes">
	    <settings mode="label" dictionarypath="" specialchar="#" option="R+S+" />
	  </Method>
	  <Method id="inner_la_cnall_sia" analysis="chinese"  casesensitive="yes">
	    <settings mode="label" dictionarypath="" specialchar="#" option="R+S+T3" />
	  </Method>
	  <Method id="la_sia" analysis="multilang"
		  advoption="default,inner_la_korall_sia;en,la_eng;cn,inner_la_cnall_sia" />
	</LanguageAnalyzer>

下面对配置中每个节点的核心属性进行简单的描述（"optional" 属性除外）：


================  ===============  ===================================================================
  元素                属性                             描述
================  ===============  ===================================================================
LanguageAnalyzer  dictionarypath   指定基于语言的分析器的辞典路径                                       
Method            id               分析器名称                                                          
     \            analysis         该属性指定分析器的类型。共有两种类型，一种是语言独立的，包括 token, ngram 和 matrix。 另外一种是语言相关的，包括 Chinese, Korean 和 其它语言。 该属性之后会跟随一些和该属性对应的一些选项。
\                 casesensitive
                  (optional)       默认值为 yes。该属性是用来设置文档字段属性的大小写敏感性。如果该属性设为 yes, 对于文件的索引和检索将区分大小写
\                 idxflag
                  (optional)       默认值为 all. Indexing flag 用来表示索引中应该返回哪种类型的词。该属性共有4种类型的值： 1） all 返回所有词; 2) prime 仅返回原始的词; 3) second 返回第二次分析得到的词; 4) none “prime” 和 “second” 两种类型的词都不返回. 原始的词是由标记解析器解析得到。 第二次得到的词是由具体的语言分析器分析得到，以解析器的解析结果作为输入。
\                 schflag 
                  (optional)       默认值 second. Searching flag 表示检索中返回那中类型的词。同上idxflag。
================  ===============  ===================================================================


**Token**

这种方法不作任何操作，仅将标记解析器解析得到的结果作为输出。所以 LAManager 的输出即是解析器的解析结果。

**NGram**

N-Grams 由分析器分析得到。 两个N的值由用户指定, 例如 N1 和 N2。举例说明： 假设 N1 = 2, N2 = 3, 对于文本 “izene”我们会得到这样的分析结果： ”iz”, ”ze”, ”en”, ”ne”, ”ize”, ”zen”, and ”ene”. 这种方法一般作为文档Title的分析方法。

==========  ==========
  属性	       描述
==========  ==========
min	    N-gram 中N的最小值
max	    N-gram中N的最大值
apart	    该选项用来告诉NGram分析器对于3组不同类型的字符要区分对待: 1) Chinese, Japanese, Korean 字符; 2)数字字符;3)字母
maxno	    该选项指定由一个标记串分析得到的词的受限个数。
==========  ==========

**Chinese Analyzer**

这种方法用来分析一个给定的中文文本，并从中抽取有用的词语。Chinese Morpheme Analysis(CMA) 用来分析中文文本，而且它也集成了英语的词干分析器。所以CMA同样可以处理中英混合的文本。

==========  ===============  ========================================
 元素          属性              描述
==========  ===============  ========================================
settings    mode	     设置分析器输出哪种类型的词语。 1)all: 原始的标记字串和分析器分析得到的词语; 2) noun: 返回分析器分析得到的词语。这种类型适合资源比较少的情况下使用； 3)label: 这种类型通常用于挖掘特性。
\           option	     下面给出详细的解释。       
\           specialchar	     相邻字母，数字，和特殊字符将被连接起来，作为一个字符被抽取。并且将自动将该字符设置为解析器的 allow选项
\           dictionarypath   覆盖 LanaguageAnalyzer选项中的辞典路径。
==========  ===============  ========================================
     
设置： 

========  =======  =========================
选项        设置     描述
========  =======  =========================
C         \+	   从复合名词中抽取名词词语
\         \*        按上面选项的方法抽取名词词语，另外，将这些名词加载到词表中。
R         0/-      返回所有的分析结果。
\         \+        使用排名最靠前的两种分析结果。
\         1-9      指定抽取多少个排名靠前的分析结果. 例如, 1 表示分析器返回最优的分析结果。2 表示分析器返回排在前两名的分析结果。
S         \-	   混合在中文文本中的英语单词会被原样抽取出来。
\         \+        对英语单词进行词干化处理。
T         1        统计方法, 这种方法具有高的正确率，但速度比较慢。
\         2        最大匹配方法，该方法具有比较高的速度，但正确率比较低。
\         3        最小匹配方法，该方法具有比较高的速度，召回率比较高，但正确率比较底。
========  =======  =========================

**Korea Analyzer**

使用KMA(Korean Morphological Analyzer)分析器抽取词语. 该分析器的选项设置和Chinese Analyzer很相似。

=========  =================   =================
元素        属性                 描述
=========  =================   =================
settings   mode	               设置分析器输出哪种类型的词语。 1)all: 原始的标记字串和分析器分析得到的词语; 2) noun: 返回分析器分析得到的词语。这种类型适合资源比较少的情况下使用； 3)label: 这种类型通常用于挖掘特性。
\          option              下面给出详细的解释。
\          specialchar         相邻字母，数字，和特殊字符将被连接起来，作为一个字符被抽取。并且将自动将该字符设置为解析器的 allow选项
\          dictionarypath      覆盖 LanaguageAnalyzer选项中的辞典路径。
=========  =================   =================

设置： 

=====================  =====================  ====================================================================
选项                    设置                   描述                                                        
=====================  =====================  ====================================================================
C                      \+	              从复合名词中抽取名词词语                                             
\                      \*                     按上面选项的方法抽取名词词语，另外，将这些名词加载到词表中。            
R                      0/-                    返回所有的分析结果。                                                 
\                      \+                     使用排名最靠前的两种分析结果。                                        
\                      1-9                    指定抽取多少个排名靠前的分析结果. 例如, 1 表示分析器返回最优的分析结果。2 表示分析器返回排在前两名的分析结果。
S                      \-                     混合在中文文本中的英语单词会被原样抽取出来。                           
\                      \+                     对英语单词进行词干化处理。                                           
N                      0                      不抽取数字                                                           
\                      1-9                    1 表示抽取包含1个或多个数字字母的数字； 9 表示抽取9个数字或者更多数字长度的数字
B                      \-                     将一个标记字符串中的数字和代表量化单位的名词分开 (米, 千克, 等) 。 如 ”10千米” = ”10”, ”千米
\                      \+                     将数字和代表量化单位的名词作为一个整体抽取出来                                               
H                      \-                     将中文字符转换为等价的韩语字符                                        
\                      \+                     如果中文字符和其对应的韩语字符一起出现，抽取出其中的中文字符。          
V                      \-                     不抽取动词和形容词的词根                                              
\                      \+                     对于具有1个以上音节的动词和形容词进行词干化处理。                      
=====================  =====================  ====================================================================



**English Analyzer (和其它的分析器)**

其它语言的分析器还包括 丹麦语, 荷兰语, 英语, 芬兰语, 法语, 德语, 匈牙利语, 意大利语, 挪威语, 葡萄牙语, 罗马尼亚语, 俄语, 西班牙语, 瑞典语, 和土耳其语。这些分析器会提供字符的词干化处理。 每个字符的词根最终作为一个检索词。词干化是查找一个词的词根的过程。例如, ”boys” => ”boy”, ”unlimited” => ”unlimit”.

**Multilanguage Analyzer**

多种语言的分析器并不是一个独立的分析器。因为多种语言混合的文档 (一个文本中至少包含2种语言)现在比较常见, 多种语言的分析器可以进行配置，利用不同的分析器处理各种不同的语言。

分析器的核心选项是advoption。该选项可以配置为 Chinese(cn), English(en), Japanese(jp) and Korean(kr)。 还有一种可选值 "default”, 表示所有的语言。

- 注意! ”default” 只能指定给一个分析器。而且指定了“default” 的分析器配置必须放在多种语言分析器前面。
- OPTIONAL. 其它语言的配置作为可选项， 每一种语言都可以采用一种处理模式。共有4中处理模式:
- “none” 不对该语言做任何处理。使用 “default” 语言分析器对该语言进行处理。
- “char“ 将该语言文本分为一个个独立的字符。例如, 采用“char” 模式处理英语文本，给定”ABC”字符串，将得到 ”A”, ”B” 和 ”C” 3个词。
- “string” 利用标点符号对该语言文本进行切分。例如,利用该模式处理英文文本，给定 ”ABC DE”字符串，将返回 ”ABC”, ”DE”两个词.
- “ma” 指定一个语言分析器对该语言文本进行处理。对不同语言的设置用分号“；”隔开。例如: advoption = ”default, inner la korall mia; cn, char”， 意指利用“char” 模式处理中文，利用innerlakorall_mia 处理其它语言。
