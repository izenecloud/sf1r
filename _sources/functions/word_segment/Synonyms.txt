同义词
========

介绍
~~~~~~~~
由于中文普遍有一义多词的同义词现象存在，例如：“电脑和计算机”，从而导致用户在检索某个特定含义时具有多种表达方式。因此，为了提高用户检索的召回率，需要对用户检索词的同义词进行同样的处理。


使用
~~~~~~~~

在Sf1r中，要起用同义词分析，可以在用户请求参数中做如下设置("use_synonym_extension"设置为true)：

::

	{
	  "collection":"chinese-wiki-dist",
	  "search":
	  {
	    "keywords":"中国",
	    "analyzer":
	    {
	      "use_synonym_extension":true,
	      "apply_la":true
	    }
	  }
	}

同义词词典资源文件位置为，[Your location]/icma/db/icwb/utf8/synonym.txt，SF1中采用了定时更新同义词词典的策略。
