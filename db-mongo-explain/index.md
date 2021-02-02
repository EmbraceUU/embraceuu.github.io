# 记一次Mongo的优化过程


记录一次Mongo的慢查询分析以及索引优化过程.

<!--more-->

### 问题描述

db.coll.find({'symbol':{$in:['OKEX.eth_btc','OKEX.btc_usdt']}}).explain('executionStats') 查询耗时

"executionTimeMillisEstimate" : 6755 为6s, 需要添加索引, 减少全表扫描

因为查询需求需要按照多个字段过滤, 避免多个字段导致在FETCH阶段耗时太多, 所以选用复合索引

首先在测试环境尝试, 再在生产环境中添加

首先需要弄清楚mongo索引的特性: mongo的索引也是使用Btree作为数据结构, 并且复合索引也需要遵照最左前缀规则[从索引组合的最左边的字段开始排序, 如果查询中没有第一个左边的字段, 将不会走索引]

### 测试过程

简历索引: db.trade_report.createIndex({'symbol':1,'account_id':1},{background: true})

查询耗时:  db.trade_report.find({'symbol':{$in:['OKEX.eth_btc','OKEX.btc_usdt']}}).explain('executionStats')

![mongo_explain_01](/mongo_explain_01.png)

可以看到, FETCH阶段不到0ms, IXSCAN阶段不到0ms; $in查询走索引

因为用到了5个字段, 所以删除当前索引, 使用多个字段测试 

db.trade_report.dropIndex('symbol_1_account_id_1')

accountId是必须的参数, cl_order_id和exec_type是选用的参数, 还有strategy_id和symbol参数

创建5个字段的索引 db.trade_report.createIndex({'account_id':1,'exec_type':1,'cl_order_id':1,'strategy_id':1,'symbol':1},{background: true})

db.trade_report.find({'account_id':'7675E0D483211CCD2184CA1CB9B3B8AB'}).explain()  走索引了

db.trade_report.find({'account_id':'7675E0D483211CCD2184CA1CB9B3B8AB','symbol':'BINANCE.BTCUSDT'}).explain() 竟然也走索引了.

颠倒顺序: db.trade_report.find({'account_id':'7675E0D483211CCD2184CA1CB9B3B8AB','symbol':'BINANCE.BTCUSDT','cl_order_id':'0e2691d6-8eef-11e8-960c-00163e0c0cdf'}).explain()  mongo在查询之前重新调整and顺序, 依旧走索引了.

去掉最左边的account_id字段, db.trade_report.find({'symbol':'BINANCE.BTCUSDT','cl_order_id':'0e2691d6-8eef-11e8-960c-00163e0c0cdf'}).explain(), stage为COLLSCAN, 没有走索引

> 不能只简单的给mongo建索引, 还需要从业务层面考虑如何建立, 比如对cl_order_id这种字段建立索引是没有必要的, 他还是需要扫描很多数据

> 而且 如果字段比索引多了还走索引吗 ?

在遵循最左前缀原则外, 另外加了side字段作为过滤
db.trade_report.find({'account_id':'D78B9B2D60E8A34504EE6AB3DCD13357-00003','strategy_id':'5a29eb73-39ad-11e9-99c8-00ff85a0533d','side':'3'}).explain()

![mongo_explain_02](/mongo_explain_02.png)

> explain results:  
>   Each stage passes its results (i.e. documents or index keys) to the parent node. The leaf nodes access the collection or the indices. The internal nodes manipulate the documents or the index keys that result from the child nodes. The root node is the final stage from which MongoDB derives the result set.    
> 子阶段返回结果给父阶段, 先开始IXSCAN, 再进行FETCH阶段 如果查询可以命中索引，它就可以直接给出满足条件的所有文档的地址（IXSCAN），由于得到的是地址，不是文档本身，所以还需要一个额外的步骤从地址找出实际的文档（FETCH）；

> explain.executionStats.executionTimeMillis:   
>   Total time in milliseconds required for query plan selection and query execution. 

> advanced:  
>   The number of intermediate results returned, or advanced, by this stage to its parent stage.  
> 返回或者推到父阶段的结果

![mongo_explain_03](/mongo_explain_03.png)

#### 排序的字段顺序需要考虑索引的顺序

添加索引示例
db.trade_report.createIndex({'exec_id':-1},{background:true,sparse:true})
此为单一字段的索引, background为后台运行,不阻塞mongo的其他操作, sparse为在索引字段存在的时候 才会有添加索引的动作,节省资源

区间查询是否走索引
db.trade_report.createIndex({'create_at':-1},{background:true})
db.trade_report.find({'create_at':{$gte:'2019-03-02 00:00:00',$lte:'2019-03-02 23:59:59'}}).explain('executionStats')

![mongo_explain_04](/mongo_explain_04.png)

测试开始: 

当前集合拥有的索引有: 

![mongo_explain_05](/mongo_explain_05.png)

命令为: db.trade_report.find({'account_id':'D78B9B2D60E8A34504EE6AB3DCD13357-00003','strategy_id':'f3b98546-3c98-11e9-8d1d-00ff85a0533d','create_at':{$gte:'2019-03-02 00:00:00',$lte:'2019-03-02 23:59:59'}}).explain('executionStats')

winner plan:

![mongo_explain_06](/mongo_explain_06.png)

![mongo_explain_07](/mongo_explain_07.png)

这是理想状态下的, 索引检索返回来的结果和filter中非索引字段过滤的结果一致

命令行: db.trade_report.find({'account_id':'D78B9B2D60E8A34504EE6AB3DCD13357-00003','strategy_id':'f3b98546-3c98-11e9-8d1d-00ff85a0533d','create_at':{$gte:'2019-03-02 19:41:14',$lte:'2019-03-02 19:41:15'}}).explain('executionStats')

winner plan :

![mongo_explain_08](/mongo_explain_08.png)

![mongo_explain_09](/mongo_explain_09.png)

![mongo_explain_10](/mongo_explain_10.png)

mongo 选用了create_at索引 , 没有走account_id_1_strategy_id_1_symbol_1索引

需要考虑索引的大小, 因为索引也是占用空间的, 太大了就没有意义了, 反而浪费资源

从数据出发，基于真实场景的日志，把业务体系里常见的查询滤出来，对最常见的查询做针对性的索引优化，然后对非常不常见的查询组合，从源头是可以控制的。
根据where条件建索引是极其重要的一个原则; 注意不要过多用索引,否则对表更新的效率有很大的影响,因为在操作表的时候要化大量时间花在创建索引中

The $match and $sort pipeline operators can take advantage of an index when they occur at the beginning of the pipeline.
聚合查询中的$match是走索引的, 需要把$match最前面的, 进入管道的数据会少很多, 并且把$sort放在$match后面会减少一部分的排序

聚合查询使用explain:  
db.col.aggregate(pepiline, options)
db.trade_report.aggregate([{$group:{_id:'$account_id',count:{$sum:1}}}],{explain:true})

查询当前accountid一共有多少:  
db.trade_report.aggregate([{$group:{_id:'$account_id',count:{$sum:1}}},{$group:{_id:'$account_id',num:{$sum:1}}}])

![mongo_explain_11](/mongo_explain_11.png)

并且account对应的数据量大小也相差较多,  少的有1个  大的有几万

![mongo_explain_12](/mongo_explain_12.png)

命令行:  db.trade_report.find({'account_id':'CE1406597976E3EF15793403649346B6'}).explain('executionStats')

![mongo_explain_13](/mongo_explain_13.png)

命令行: db.trade_report.find({'account_id':'CE1406597976E3EF15793403649346B6','strategy_id':'7ab8d8dd-3a6f-11e9-acd2-0e04d673df9e'}).limit(1).explain('executionStats')

![mongo_explain_14](/mongo_explain_14.png)

因为业务上每次都会有accountid和strategyid,所以这两个应该建立一个针对性的窄索引; 然后加上Limit 500, 根据id逆序排序
