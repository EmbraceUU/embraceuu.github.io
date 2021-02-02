# Mongo Note


<!--more-->

## mongodb

#### 数据自动过期

使用 pymongo 和 mongo index

```
# 在python中insert时带上expire_time
doc = {
    '_id': ObjectId(),
    ...
    'expire_time': datetime.datetime.utcnow()
}

# 在collection中添加 index, 并带有过期属性, 单位为s
db.collection.createIndex({"expire_time": 1}, {expireAfterSeconds: NumberInt("691200")});
```

mongo 会有一个专门的进程扫描带有expire_time字段的数据, 通过对比里面的时间, 判断数据是否需要清除.

#### 更改某字段为另外一个字段的值

```
db.info.find().forEach(
  function(item){                 
      db.info.update({"_id":item._id},{"$set": {"symbol_dsp":item.symbol}}) 
    }
)
```

#### 更改某字段, 对字段做一些处理

```
db.symbol_info.find({'exchange': 'cococoin', 'symbol_type': 1}).forEach(
  function(item){  
      item.rest_symbol = item.rest_symbol.replace("661.", "")
      db.symbol_info.update({"_id":item._id},{"$set": {"rest_symbol":item.rest_symbol}}) 
    }
)
```

#### 跨表修改字段的值

```
db.coin_mapping.find({"status":1}).forEach(
    function(x){
        db.info.update({'_id': x.mars_coin_id}, {$set: {'qkl123_id': NumberInt(x.qkl123_coin_id)}})
    }
)
```

#### 查重

```
db.duplicate_coin.aggregate([
    { $group: { _id : '$symbol', count: { $sum : 1 } } },
    { $match: { count: { $gt : 1} } }
])

## 多字段批量查询
db.symbol_info.aggregate([
    { $group: { _id : {symbol: '$symbol', ex: '$exchange', status: '$status'}, count: { $sum : 1 } } },
    { $match: { count: { $gt : 1} } }
])
```

#### 字段类型

```
## 查看字段类型
db.symbol_info.find({'status': {$type: 1}}).count()   

## 批量修改字段类型
db.symbol_info.find({"status":{$type:1}}).forEach(function(x){x.status=NumberInt(x.status);db.symbol_info.save(x)})
```

#### 事务

https://www.jianshu.com/p/d838a5905303

#### 索引

```
db.symbol_info.createIndex({'exchange': 1, 'symbol': 1}, {unique: true})
```
