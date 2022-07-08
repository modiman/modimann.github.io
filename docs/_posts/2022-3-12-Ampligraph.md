



# 文档

## 如何贡献

### Git Repo and Issue Tracking

* <a href= "https://github.com/Accenture/AmpliGraph">AmpliGraph</a>
* <a href="https://github.com/Accenture/AmpliGraph/issues">未解决问题列表</a>
* <a href="https://join.slack.com/t/ampligraph/shared_invite/enQtNTc2NTI0MzUxMTM5LTRkODk0MjI2OWRlZjdjYmExY2Q3M2M3NGY0MGYyMmI4NWYyMWVhYTRjZDhkZjA1YTEyMzBkMGE4N2RmNTRiZDg">在Slack上讨论</a>

### How to Contribute

我们欢迎社区贡献，无论是新模型、测试还是文档。

您可以通过多种方式为 AmpliGraph 做出贡献：

* [报告bug](https://github.com/Accenture/AmpliGraph/issues/new?assignees=&labels=&template=bug_report.md&title=)
* [提交功能请求](https://github.com/Accenture/AmpliGraph/issues/new?assignees=&labels=&template=feature_request.md&title=)
*  [在issue tracking system评论来帮助他人](https://github.com/Accenture/AmpliGraph/issues)
* 增加单元测试
* 改进文档
* 添加新模型（往下看）

### 添加自己的模型

知识图谱嵌入发展迅速。AmpliGraph的建立是为了提供一个共享的代码库，以保证对所有模型进行公平的评估和比较。我们欢迎新模型作为对AmpliGraph的贡献

可以通过pull request提交自己的模型

开始之前可以先阅读API文档

### 开发者笔记

有关数据适配器、大型图形的AmpliGraph支持的其他文档，以及更多技术细节，请参见此处。[开发文档](https://docs.ampligraph.org/en/1.4.0/dev_notes.html)

### 以可编辑模式克隆安装

克隆仓库并检出 `develop` 分支. 使用 `-e`开启 [可编辑模式](https://pip.pypa.io/en/stable/reference/pip_install/#editable-installs):

```bash
git clone https://github.com/Accenture/AmpliGraph.git
git checkout develop
cd AmpliGraph
pip install -e .
```

### 单元测试

```bash
pytest tests
```

See [pytest documentation](https://docs.pytest.org/en/latest/) for additional arguments.

### 打包

要构建 AmpliGraph 自定义wheel，请执行以下操作：

```bash
pip wheel --wheel-dir dist --no-deps .
```



 ## dataloader



# 源码分析

### 参数说明

* `verbose `bool类型，是否显示训练进度条
* `X `: ndarray, shape [n, 3]       一个存放测试三元组的数组
* `model` : EmbeddingModel      知识图嵌入模型
* 

### ampligraph.evaluation.evaluate_performance

```python
def evaluate_performance(X, model, filter_triples=None, verbose=False, filter_unseen=True, entities_subset=None,
                         corrupt_side='s,o', ranking_strategy='worst', use_default_protocol=False):
    """Evaluate the performance of an embedding model.
       评估嵌入模型的性能
       评估协议遵循bordes2013translating中的过程，概括如下：

    先打乱head再打乱tail，手动生成负例三元组

    #. Remove the positive triples from the set returned by (1) -- positive triples \
    are usually the concatenation of training, validation and test sets.
    从（1）——正三元组返回的集合中删除正三元组\\通常是训练集、验证集和测试集的拼接。

    #. 根据（2）返回的所有剩余三元组对每个测试三元组进行排序。


    With the ranks of both object and subject corruptions, one may compute metrics such as the MRR by
    calculating them separately and then averaging them out.
    Note that the metrics implemented in AmpliGraph's ``evaluate.metrics`` module will already work that way
    when provided with the input returned by ``evaluate_performance``.

    The artificially generated negatives are compliant with the local closed world assumption (LCWA),
    as described in :cite:`nickel2016review`. In practice, that means only one side of the triple is corrupted at a time
    (i.e. either the subject or the object).

    .. note::
        The evaluation protocol assigns the worst rank
        to a positive test triple in case of a tie with negatives. This is the agreed upon behaviour in literature.

    .. hint::
        When ``entities_subset=None``, the method will use all distinct entities in the knowledge graph ``X``
        to generate negatives to rank against. This might slow down the eval. Some of the corruptions may not even
        make sense for the task that one may be interested in.

        For eg, consider the case <Actor, acted_in, ?>, where we are mainly interested in such movies that an actor
        has acted in. A sensible way to evaluate this would be to rank against all the movie entities and compute
        the desired metrics. In such cases, where focus us on particular task, it is recommended to pass the desired
        entities to use to generate corruptions to ``entities_subset``. Besides, trying to rank a positive against an
        extremely large number of negatives may be overkilling.

        As a reference, the popular FB15k-237 dataset has ~15k distinct entities. The evaluation protocol ranks each
        positives against 15k corruptions per side.

    c参数
    ----------
    
 
    filter_triples : ndarray of shape [n, 3] or None
        用于过滤掉负例的三元组

        .. note::
            When *filtered* mode is enabled (i.e. `filtered_triples` is not ``None``),
            to speed up the procedure, we use a database based filtering. This strategy is as described below:

            * Store the filter_triples in the DB在数据库中存储 filter_triples
            * 对于每个测试三元组, 生成用于评估的负例并为她们打分
            
            * corruptions可能包含错误i的负例，通过查询数据库可以找到它们
            * From the computed scores we retrieve the scores of the False Negatives.
            * 通过比较所有的 corruptions计算测试三元组的排名
            * 计算错误负例打分超过测试三元组的数量，然后从上面计算的排名中减去这个值，得到最终的过滤排名。
             

            **Execution Time:** This method takes ~4 minutes on FB15K using ComplEx
            (Intel Xeon Gold 6142, 64 GB Ubuntu 16.04 box, Tesla V100 16GB)
    filter_unseen : bool
        如果使用了train_test_split_unseen（），则可以将其设置为False，以跳过对不可见实体的过滤拆分原始数据集。

    entities_subset: array-like
        List of entities to use for corruptions. If None, will generate corruptions
        using all distinct entities. Default is None.
    corrupt_side: string
        Specifies which side of the triple to corrupt:

        - 's': corrupt only subject.
        - 'o': corrupt only object.
        - 's+o': corrupt both subject and object.
        - 's,o': corrupt subject and object sides independently and return 2 ranks. This corresponds to the \
        evaluation protocol used in literature, where head and tail corruptions are evaluated separately.

        .. note::
            When ``corrupt_side='s,o'`` the function will return 2*n ranks as a [n, 2] array.
            The first column of the array represents the subject corruptions.
            The second column of the array represents the object corruptions.
            Otherwise, the function returns n ranks as [n] array.

    ranking_strategy: string
        Specifies the type of score comparison strategy to use while ranking:

        - 'worst': assigns the worst rank when scores are equal
        - 'best': assigns the best rank when scores are equal
        - 'middle': assigns the middle rank when scores are equal

        Our recommendation is to use ``worst``.
        Think of a model which assigns constant score to any triples. If you use the ``best`` strategy then 
        the ranks will always be 1 (which is incorrect because the model has not learnt anything). If you choose 
        this model and try to do knowledge discovery, you will not be able to deduce anything as all triples will 
        get the same scores. So to be on safer side while choosing the model, we would recommend either ``worst``
        or ``middle`` strategy.

    use_default_protocol: bool
        Flag to indicate whether to use the standard protocol used in literature defined in
        :cite:`bordes2013translating` (default: False).
        If set to `True`, ``corrupt_side`` will be set to `'s,o'`.
        This corresponds to the evaluation protocol used in literature, where head and tail corruptions
        are evaluated separately, i.e. in corrupt_side='s,o' mode

    Returns
    -------
    ranks : ndarray, shape [n] or [n,2] depending on the value of corrupt_side.
        An array of ranks of test triples.
        When ``corrupt_side='s,o'`` the function returns [n,2]. The first column represents the rank against
        subject corruptions and the second column represents the rank against object corruptions.
        In other cases, it returns [n] i.e. rank against the specified corruptions.
    """

    from ampligraph.latent_features import ConvE  # avoids circular import hell

    dataset_handle = None

    # try-except block is mainly to handle clean up in case of exception or manual stop in jupyter notebook
    try:
        if use_default_protocol:
            logger.warning('DeprecationWarning: use_default_protocol will be removed in future. '
                           'Please use corrupt_side argument instead.')
            corrupt_side = 's,o'

        logger.debug('Evaluating the performance of the embedding model.')
        assert corrupt_side in ['s', 'o', 's+o', 's,o'], 'Invalid value for corrupt_side.'
        if isinstance(X, np.ndarray):

            if filter_unseen:
                X = filter_unseen_entities(X, model, verbose=verbose)
            else:
                logger.warning("If your test set or filter triples contain unseen entities you may get a"
                               "runtime error. You can filter them by setting filter_unseen=True")

            if isinstance(model, ConvE):
                dataset_handle = OneToNDatasetAdapter()
            else:
                dataset_handle = NumpyDatasetAdapter()

            dataset_handle.use_mappings(model.rel_to_idx, model.ent_to_idx)
            dataset_handle.set_data(X, 'test')

        elif isinstance(X, AmpligraphDatasetAdapter):
            dataset_handle = X
        else:
            msg = "X must be either a numpy array or an AmpligraphDatasetAdapter."
            logger.error(msg)
            raise ValueError(msg)

        if filter_triples is not None:
            if isinstance(filter_triples, np.ndarray):
                logger.debug('Getting filtered triples.')

                if filter_unseen:
                    filter_triples = filter_unseen_entities(filter_triples, model, verbose=verbose)
                dataset_handle.set_filter(filter_triples)
                model.set_filter_for_eval()
            elif isinstance(X, AmpligraphDatasetAdapter):
                if not isinstance(filter_triples, bool):
                    raise Exception('Expected a boolean type')
                if filter_triples is True:
                    model.set_filter_for_eval()
            else:
                raise Exception('Invalid datatype for filter. Expected a numpy array or preset data in the adapter.')

        eval_dict = {}

        # #186: print warning when trying to evaluate with too many entities.
        #      Thus will likely result in shooting in your feet, as the protocol will be excessively hard.
        check_filter_size(model, entities_subset)

        if entities_subset is not None:
            idx_entities = np.asarray([idx for uri, idx in model.ent_to_idx.items() if uri in entities_subset])
            eval_dict['corruption_entities'] = idx_entities

        logger.debug('Evaluating the test set by corrupting side : {}'.format(corrupt_side))
        eval_dict['corrupt_side'] = corrupt_side

        assert ranking_strategy in ['worst', 'best', 'middle'], 'Invalid ranking_strategy!'

        eval_dict['ranking_strategy'] = ranking_strategy

        logger.debug('Configuring evaluation protocol.')
        model.configure_evaluation_protocol(eval_dict)

        logger.debug('Making predictions.')
        ranks = model.get_ranks(dataset_handle)

        logger.debug('Ending Evaluation')
        model.end_evaluation()

        logger.debug('Returning ranks of positive test triples obtained by corrupting {}.'.format(corrupt_side))
        return np.array(ranks)

    except BaseException as e:
        model.end_evaluation()
        if dataset_handle is not None:
            dataset_handle.cleanup()
        raise e
```