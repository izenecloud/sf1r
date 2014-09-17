FuzzySearch
========

**1. 介绍**

  与倒排索引支持精确查询不同，模糊搜索用于获得与检索词最接近的文档；其通常用于较长的检索词的搜索，例如：基于商品标题的搜索。

**2. 构建**

  FuzzyIndex是基于小波树构建， 在构建时需要占用大量的内存。 

  因此，在构建FuzzyIndex时，系统需要保留足够的内存空间，以避免内存空间耗尽；

**3. 更新**

 由于小波树不支持增量构建；因此，FuzzyIndex更新时，对于不同类型的SCD构建策略有所不同；

 **3.1 -U 更新**

 对于-U SCD更新，fuzzyIndex需要重新构建，其filter也需要重新构建。

 **3.2 -D 更新**

 对于-D SCD更新，fuzzyIndex需要重新构建，其filter也需要重新构建。

 **3.3 -R 更新** 

 由于Rtype字段字段是非FuzzyIndex索引字段，例如，价格字段。因此，在进行Rtype更新时，虽然原文档已经发生了变化，但fm-index不需要更新，而是更新fm-index对应的filter; 

**4. filter**

  filter用来对FuzzyIndex检索结果的过滤。与此同时，由于fuzzyIndex仅对title字段建立索引，可以将filter作为其他字段的索引，用于基于其他字段的查询，例如，Source字段。

