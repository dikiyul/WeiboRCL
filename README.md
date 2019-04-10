# 竞赛题目

对于一条原创博文而言,转发、评论、赞等互动行为能够体现出用户对于博文内容的兴趣程度，也是对博文进行分发控制的重要参考指标。**本届赛题的任务就是根据抽样用户的原创博文在发表一周后的转发、评论、赞总数，建立博文的互动模型，并预测用户后续博文在发表一周后的互动情况。**

## 数据说明

### 1. 训练数据（weibo_train_data(new)）

**2015-02-01至2015-07-31**博文的全部信息都映射为一行数据。其中对用户做了一定抽样，获取了抽样用户半年的原创博文，对用户标记和博文标记做了加密 发博时间精确到天级别。 

| 字段 | 字段说明 |    字段说明   |
|:----:|:--------:|:-------------:|
|  uid | 用户标记 | 抽样&字段加密 |
|  mid | 博文标记 | 抽样&字段加密 |
| time | 发博时间 | 精确到天 |
| forward_count | 博文发表一周后的转发数 |    |
| comment_count | 博文发表一周后的评论数 |    |
| like_count | 博文发表一周后的赞数 |    |
| content | 博文内容 |    |

### 2. 预测数据（weibo_predict_data(new)）

**2015-08-01至2015-08-31**

| 字段 | 字段说明 |    字段说明   |
|:----:|:--------:|:-------------:|
|  uid | 用户标记 | 抽样&字段加密 |
|  mid | 博文标记 | 抽样&字段加密 |
| time | 发博时间 | 精确到天 |
| content | 博文内容 |    |

### 3. 选手需要提交的数据（weibo_result_data）

选手对预测数据（weibo_predict_data）中每条博文一周后的转、评、赞值进行预测。

| 字段 | 字段说明 |    字段说明   |
|:----:|:--------:|:-------------:|
|  uid | 用户标记 | 抽样&字段加密 |
|  mid | 博文标记 | 抽样&字段加密 |
| forward_count | 博文发表一周后的转发数 |    |
| comment_count | 博文发表一周后的评论数 |    |
| like_count | 博文发表一周后的赞数 |    |

选手提交结果文件的转、评、赞值必须为整数不接受浮点数！注意：提交格式(.txt)：uid、mid、forward_count字段以tab键分隔，forward_count、comment_count、like_count字段间以逗号分隔。

## 初赛评估指标

参赛队对于每一条博文预测出发表一周后的转发数、评论数和赞的数目，对于每一项均和真实值计算偏差：

转发偏差：
<font size=6>$deviation_f = \frac{|count_{fp} - count_{fr}|}{count_{fr} + 5}$</font>

评论偏差：
<font size=6>$deviation_c = \frac{|count_{cp} - count_{cr}|}{count_{cr} + 3}$</font>

赞偏差：
<font size=6>$deviation_l = \frac{|count_{lp} - count_{lr}|}{count_{lr} + 3}$</font>

注：
1. $count_{fp}$为预测的转发数，$count_{fr}$为实际的转发数；
2. $count_{cp}$为预测的评论数，$count_{cr}$为实际的评论数；
1. $count_{lp}$为预测的赞数，$count_{lr}$为实际的赞数。

综合上述三项偏差，计算第i篇博文的准确率：

<font size=6>$precision_i = 1 - 0.5 \times deviation_f - 0.25 \times deviation_c - 0.25 \times deviation_l$</font>

最后，计算整体的计算率：

<font size=6>$precision = \frac{\sum_{i}^{N}(count_i + 1) \times sgn(precision_i - 0.8) }{\sum_{i}^{N}(count_i + 1)}$</font>

$其中：sgn(x)为改进的符号函数，当x>0时， sgn(x)=1，当x<=0时，sgn(x)=0。$

$count_i为第i篇博文的总的转发、评论、赞之和，当count_i>100时，取值为100。$

# 解决思路

## 1. 特征提取
 - 用户特征
    - 发微博总条数
    - 所有微博最大/最小/平均/中位 被评论数、点赞数、转发数    
    - 0/1-10/10-100/100-1000/1000+ 转/评/赞 占发微博总条数的比例

 - 时间特征
 - 博文特征