# EDGE: Enriching Knowledge Graph Embeddings with External Text

（使用外部文本丰富知识图谱嵌入）

引入外部文本构建一个知识图谱超图（即知识图谱是其子图）----增强知识图谱，学习超图中的实体嵌入，将此嵌入与原图谱中的实体嵌入对齐，用来做链路预测以及实体分类任务

## 增强知识图谱构建

​     	增强是指向知识图谱中添加新实体的过程，新添加的实体叫做文本实体或文本节点。这些新的实体有一个重要的属性-----它们包含原知识图谱中的实体。这些实体的存在建立给出两个图之间的关系，并且这种关系将被用来学习共享图嵌入。

​        为了构建这个增强图，需要搜集一些关键字来查询对应的文本。具体过程：

1. 找到一批具有相似语义和结构的实体集εe,这一集合用来从WordNet或Wikipedia中查找外部文本S （使用API wikipedia）
2. 从S中提取实体并连接到εe,通过发现新实体连接到旧实体，加强了KG并产生了增强知识图谱

**问题**：由于一段文本中可能出现不同实体，故没有连接的文本实体距离也会很小，这会产生一些噪声，稍后的对齐一步可以处理这一问题

## 联合嵌入空间中的知识图对齐
​       在扩充知识图aKG的帮助下，我们的目标是丰富知识图的嵌入。然而，不可避免的是，一部分新添加的实体是嘈杂的，甚至可能是错误的。为了缓解这个问题，受到了Hinton等人的启发。

​         特别地，我们提出利用图自动编码器提取两个知识图的低维节点嵌入
<img src='https://github.com/modiman/modiman.github.io/blob/gh-pages/docs/_posts/imgs/image-20211115175943950.png?raw=true'/>

## 实验设置

通过实验需要回答的三个问题

* 模型与链路预测当前最佳模型性能差距
* 生成的嵌入与类似方法比较如何
* 增强和对齐各自对模型整体性能的影响

### 任务1 链路预测
<img src='https://github.com/modiman/modiman.github.io/blob/gh-pages/docs/_posts/imgs/image-20211115180514574.png?raw=true'/>

### 任务2 节点分类
<img src='https://github.com/modiman/modiman.github.io/blob/gh-pages/docs/_posts/imgs/image-20211115180642725.png?raw=true'/>

### 任务3 嵌入效果
<img src='https://github.com/modiman/modiman.github.io/blob/gh-pages/docs/_posts/imgs/image-20211115180757214.png?raw=true'/>

