# 控制相关度(Controlling Relevance) #

对于仅处理结构化数据(比如日期，数值和字符枚举值)的数据库，它们只需要检查一份文档(在关系数据库中是一行)是否匹配查询即可。

尽管布尔类型的YES|NO匹配也是全文搜索的一个必要组成，它们本身是不够的。我们还需要知道每份文档和查询之间的相关程度。全文搜索引擎不仅要找到匹配的文档，还需要根据相关度对它们进行排序。

全文搜索相关度的公式，或者被称为相似度算法，将多个因素综合起来为没份文档产生一个相关度_score。在本章中，我们来讨论一下其中的一些变化的部分以及如何控制它们。

当然相关度并不只和全文查询相关；它也许会将结构化数据考虑在内。我们可能在寻找一个拥有某些卖点(空调，海景，免费的WiFi)的度假旅店。那么当某个度假旅店拥有的卖点越多，那么它也就越相关。或者我们希望除全文搜索本身的相关度外，同时将时间的远近，价格，流行度或者距离这类因素也考虑在内。

以上这些设想都是可以实现的，得益于ES中强大的分值计算功能。

我们首先会从理论角度来看看Lucene是如何计算相关度的，然后从实际的例子出发来讨论一下如何来控制该过程。