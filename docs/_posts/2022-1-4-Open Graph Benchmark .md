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



# PairRE: Knowledge Graph Embeddings via Paired Relation Vectors

## 摘要

为了处理N-to-1、1-to-N和N-to-N这类复杂关系，提出PairRE模型，这是一个对每个关系表示都有成对向量的模型。PairRE能够编码三种重要的关系模式:对称/反对称、逆和复合。给定关系表示的简单约束，PairRE可以进一步编码子关系。在链路预测基准上的实验验证了PairRE提出的关键功能。此外，我们在具有挑战性的Open  graph Benchmark的两个知识图数据集上建立了新的技术水平。

## 方法

使用向量对[r<sup>H</sup>，r<sup>T</sup>]来表示关系嵌入，

r<sup>H</sup>和r<sup>T</sup>分别把头实体head和尾实体投影到欧几里得向量空间

投影运算是两个向量的Hadamard积

PairRE接着计算两个向量的距离作为三元组的可信度，本文使用使用L1范式计算距离

如果h和t在同一三元组（h,r,t）中,希望                 

​                                                         **h○r<sup>H</sup>≈t○r<sup>T</sup>**

否则希望两者尽可能的远离彼此

为了移除scaling freedoms，增加了TransE中的约束，但约束只加给实体嵌入

希望关系嵌入能够简单但充分地捕获关系向量的语义联系和复杂特性，

打分函数定义为

f(h,t) = -||   **h○r<sup>H</sup>≈t○r<sup>T</sup>**||,   ||H|| = ||T|| = 1



