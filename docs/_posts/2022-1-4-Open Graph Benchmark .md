# Open Graph Benchmark

* https://ogb.stanford.edu/
* <a href=" https://zhuanlan.zhihu.com/p/165996331">论文阅读 | 图学习百万量级基准数据集OGB：Open Graph Benchmark - 何明国的文章 - 知乎</a>

**OGB的特点**：

- 规模大。分为small、medium、large三个规模，具体为small：超过10万个节点和超过100万条边；medium：超过100万个节点或超过1000万条边；large：大约1亿个节点或10亿条边。
- 多领域。nature：包含生物网络和分子图；society：包含学术图和电子商务网络；information：包含[知识图谱](https://www.zhihu.com/search?q=知识图谱&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"165996331"})等。
- 多任务。节点属性预测，链接属性预测，图属性预测。

当前，OGB总共包括15个数据集，每个任务类别至少有4个数据集。

* 节点预测
* 链路预测
* 图预测



# TripleRE: Knowledge Graph Embeddings via triple Relation Vectors
* 奇虎360
* 西安交大

现有的方法有的使用平移模型，有的使用投影模型，该文章提出的**TripleRE模型**同时使用平移和投影，在ogbl-wikikg2数据集上获得了最好结果

## 模型

两个版本，v1和v2



