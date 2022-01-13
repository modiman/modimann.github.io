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

现有的方法有的使用平移模型，有的使用投影模型，该文章提出的**TripleRE模型**同时使用平移和投影，在ogbl-wikikg2数据集上获得了最好结果，该模型基于PairRE进行改进

## 模型

两个版本，v1和v2



# PairRE: Knowledge Graph Embeddings via Paired Relation Vectors

## 摘要

为了处理N-to-1、1-to-N和N-to-N这类复杂关系，提出PairRE模型，这是一个对每个关系表示都有成对向量的模型。PairRE能够编码三种重要的关系模式:对称/反对称、逆和复合。给定关系表示的简单约束，PairRE可以进一步编码子关系。在链路预测基准上的实验验证了PairRE提出的关键功能。此外，我们在具有挑战性的Open  graph Benchmark的两个知识图数据集上建立了新的技术水平。

## 优点

* 成对关系表示使损失函数的裕度能够自适应调整，以适应不同的复杂关系;
* 更好地捕捉语义联系，使得对 对称/反对称、逆关系和复合关系的嵌入更加合理
* 在关系表示中添加简单的约束，PairRE可以进一步编码子关系。

## 背景和符号


参考Wang et al., 2014，对关系进行简单划分
对于一个关系r,定义两个符号
* tphr：头实体对应尾实体数量的平均值
举例：
```
a,r,b
a,r,c
a,r,d
h,r,c
h,r,e
```
头实体a对应3个尾实体
头实体h对应2个尾实体
tphr = (3+2)/2 = 2.5


* hptr 尾实体对应头实体数量的平均值

**定义**

如果 tphr <1.5 && hptr < 1.5, r为1-1关系
如果 tphr >1.5 && hptr > 1.5, r为N-N关系
如果 tphr >1.5 && hptr < 1.5, r为1-N关系


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
```
f(h,t) = -||   **h○r<sup>H</sup>≈t○r<sup>T</sup>**||,   其中||H|| = ||T|| = 1

```
## 实验

### 评估协议


使用MR，MRR,Hit@N三个评估矩阵 

### 实现
在相应的实验中使用了ogbl-wikikg2和ogblbiokg (Hu et al.， 2020)基准测试的官方实现


只对超参数γ和嵌入维度进行了微调，其他的设置都和baseline相同，PairRE以外的实验参考RotatE模型中的方法进行

# 数据集
## ogbl-biokg
### 概述
ogbl-biokg数据集是一个知识图(kg)，它是我们使用大量生物医学数据仓库的数据创建的。它包含5种类型的实体:

|结点类型|疾病|蛋白质|药物|副作用|蛋白质功能|
|--|--|--|--|--|--|
|结点数量|10687|17499|10533|9969|45085|



### 关系


连接两类实体的有向关系共有51种，包括39种药物-药物相互作用、8种蛋白质-蛋白质相互作用，以及药物-蛋白质、药物-副作用、药物-蛋白质、功能-功能关系。所有关系都被建模为有向边，其中连接相同实体类型(例如，蛋白质-蛋白质、药物-药物、功能-功能)的关系总是对称的，即边是双向的。
### 研究意义
该数据集与生物医学和基础ML研究都相关。在生物医学方面，该数据集使我们能够更好地了解人类生物学，并生成可以指导下游生物医学研究的预测。在最基本的最大似然方面，数据集在处理可能有矛盾观测值的噪声、不完整的KG时带来了挑战。这是因为ogbl-biokg数据集涉及从分子规模(例如，细胞内的蛋白质-蛋白质相互作用)到整个群体(例如，特定国家患者经历的不良副作用的报告)的异质性相互作用。此外，KG中的三元组来自具有各种置信水平的来源，包括实验读数、人工辅助注释和自动提取的元数据。

### 预测任务:
任务是预测给定训练三元组的新三元组。评估协议与ogbl-wiki G2完全相同，只是这里我们只考虑针对相同类型的实体进行排名。例如，当腐蚀蛋白质类型的头部实体时，我们只考虑负蛋白质实体。
### 数据集分割
对于这个数据集，我们采用随机分割。虽然根据时间分割三元组是一个有吸引力的选择，但我们注意到，获得关于三元组背后的单个实验和观察是何时进行的准确信息是非常具有挑战性的。我们努力在OGB的未来版本中提供额外的数据集分割


昨天以为装上了，实际装obg失败了，原因是卡在了torch安装这一步
今天单独安装torch ，因为是在windows上配置环境，在安装torch时按选择没有cuda的cpu-only版本
命令
```
conda install pytorch torchvision torchaudio cpuonly -c pytorch

```
网上说一般人学习只用cpu-only的torch版本就可以，希望没问题
pairre模型用的是16G显存的显卡，试试我的amd cpu顶不顶
现在在下数据集，希望能成功，
运行失败，源代码调用了cuda，本机没有

从run.py看代码

代码细节
### defaultdict
```python

from collections import defaultdict

train_count, train_true_head, train_true_tail = defaultdict(lambda: 4), defaultdict(list), defaultdict(list)

```
defaultdict区别于普通的dict
对于普通的dict，调用不存在的键值对时会报错
```python
>>> a = dict()
>>> a['name'] = 'modige'
>>> print(a['name'])
modige
>>> print(a['age'])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'age'

```
但对于defaultdict

```python
>>> b = defaultdict(list)
>>> print(b['age'])
[]
>>> c = defaultdict(tuple)
>>> print(c['age'])
()
```
可以看出，但调用不存在的key时会返回一个形参类型的空结果

### tqdm
Tqdm 是一个快速，可扩展的Python进度条，可以在 Python 长循环中添加一个进度提示信息，用户只需要封装任意的迭代器 tqdm(iterator)。



使用方法
```python
    for i in tqdm(range(len(train_triples['head']))):
        head, relation, tail = train_triples['head'][i], train_triples['relation'][i], train_triples['tail'][i]
        head_type, tail_type = train_triples['head_type'][i], train_triples['tail_type'][i]
        train_count[(head, relation, head_type)] += 1
        train_count[(tail, -relation-1, tail_type)] += 1
        train_true_head[(relation, tail)].append(head)
        train_true_tail[(head, relation)].append(tail)
```
样式如下

100%|██████████| 4762678/4762678 [00:14<00:00, 335575.16it/s]

或
```python
from tqdm import tqdm
for i in tqdm(range(1000)):

     #do something
     pass
```
### 代码主要流程
1. 如果没有指定参数，给出提示
2. 检查是否从断点处启动
3. 设置模型保存路径
4. 载入数据集
5. 划分数据集
7. 定义模型参数
8. 声明模型
9. 开始训练
10. 测试模型
11. 验证模型
11. 输出结果
