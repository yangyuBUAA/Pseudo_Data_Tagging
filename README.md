### 伪标签方式进行数据标注

1. 将标签数据放入dataset/tagged/目录下并命名为label.txt
2. 将未标记数据放入dataset/source/目录下并命名为unlabeled.txt
3. 运行run_psedo_labeling.sh

##### 注：默认为二分类，label.txt中的数据为"sentence\t1" or "sentence\t0"，如果想要更改为多分类，那么需要修改模型。运行算法之前需要在config.yaml中修改配置参数

---
##### 标注记录
- 预训练模型：chinese-roberta-wwm-ext-large
- batch_size：1024 （p40 24GB * 4）
- learning rate：0.000002
- 标注样本：10w
- 训练集测试集划分比例：0.8
- 最大迭代次数：10
- 标注阈值：0.8
---
##### 每个轮次标注情况
1. 0iteration：
- 人工标注数据：10w
- 自动标注数据：0
- 训练集8w，验证集2w
- 未标注数据：30w
- 标注前验证集情况：
- 2021-08-03 06:57:31,473 - INFO - eval accu: 0.8764
- 2021-08-03 06:57:31,473 - INFO - confuse_matrix:
- 2021-08-03 06:57:31,473 - INFO - 正样本8457.0 |1633.0 |
- 2021-08-03 06:57:31,473 - INFO - 负样本839.0 |9071.0 |
- 2021-08-03 06:57:31,473 - INFO - 正样本 精度 0.9097461273666093 召回率 0.8381565906838454
- 2021-08-03 06:57:31,473 - INFO - 负样本 精度 0.8474402092675636 召回率 0.9153380423814329
- 标注耗时：3h45min
- 标注成功:225456;标注失败:74544条数据
2. 1iteration：
- 人工标注数据：10w
- 自动标注数据：225456
- 训练集：260364，验证集65092
- 未标注数据：74544
- 标注前验证集情况：
- 2021-08-03 06:57:31,473 - INFO - eval accu: 0.95898116
- 2021-08-03 06:57:31,473 - INFO - confuse_matrix:
- 2021-08-03 06:57:31,473 - INFO - 正样本29967.0 |2030.0 |
- 2021-08-03 06:57:31,473 - INFO - 负样本640.0 |32455.0 |
- 2021-08-03 06:57:31,473 - INFO - 正样本 精度 0.9790897507106218 召回率 0.9365565521767666
- 2021-08-03 06:57:31,473 - INFO - 负样本 精度 0.9411338263012904 召回率 0.9806617313793624
- 标注耗时：43min25s
- 标注成功:51326;标注失败:23218条数据
3. 2iteration：
- 人工标注数据：10w
- 自动标注数据：276782
- 训练集：301425，验证集75357
- 未标注数据：23218
- 标注前验证集情况：
- 2021-08-03 06:57:31,473 - INFO - eval accu: 0.95817244
- 2021-08-03 06:57:31,473 - INFO - confuse_matrix:
- 2021-08-03 06:57:31,473 - INFO - 正样本34453.0 |2229.0 |
- 2021-08-03 06:57:31,473 - INFO - 负样本923.0 |37752.0 |
- 2021-08-03 06:57:31,473 - INFO - 正样本 精度 0.9739088647670737 召回率 0.9392345019355542
- 2021-08-03 06:57:31,473 - INFO - 负样本 精度 负样本 精度 0.9442485180460719 召回率 0.9761344537815126
- 标注耗时：17min23s
- 标注成功:5105;标注失败:18113条数据

##### 标注前后分类性能对比

---
- 人工标记数据10w条，划分出1w的测试数据
- 使用9w人工标记数据训练模型（包括训练集和验证集）
- 使用9w+pseudo label数据训练模型（包括训练集和验证集）
- 比较模型在1w人工标记数据中的指标
- 结果1：在1w人工标记数据中的指标大数据集略高，对于分类有一定的性能提升
- 结果2：在两个模型的训练过程中，大数据集中验证集的准召率显著高于小数据集

- 分类指标没有下降表明伪标签生成的数据没有产生太大的偏移，可以使用伪标签进行数据标注提升性能，但是迭代轮次不宜过多，因为比较难分的数据可能属于噪声数据，需要对这部分数据进行分析，进行badcase的处理，而不是迭代将其进行可能的错误标注。

