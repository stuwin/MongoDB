##### MongoDB常用语句，顺带与SQL做个简单的对比。
1. 查询(find)

（1）查询所有结果
```
select * from article
db.article.find()
```
（2）指定返回哪些键
```
select title, author from article
db.article.find({}, {"title": 1, "author": 1})
```
（3）where条件
```
select * from article where title = "mongodb"
db.article.find({"title": "mongodb"})
```
（4）and条件
```
select * from article where title = "mongodb" and author = "god"
db.article.find({"title": "mongodb", "author": "god"})
```
（5）or条件
```
select * from article where title = "mongodb" or author = "god"
db.article.find({"$or": [{"title": "mongodb"}, {"author": "god"}]})
```
（6）比较条件
```
select * from article where read >= 100;
db.article.find({"read": {"$gt": 100}})

$gt(>)、$gte(>=)、$lt(<)、$lte(<=)

select * from article where read >= 100 and read <= 200
db.article.find({"read": {"$gte": 100, "lte": 200}})
```
（7）in条件
```
select * from article where author in ("a", "b", "c")
db.article.find({"author": {"$in": ["a", "b", "c"]}})
```
(8) like
```
select * from article where title like "%mongodb%"
db.article.find({"title": /mongodb/})
```
（9）count
```
select count(*) from article
db.article.count()
```
（10）不等于
```
select * from article where author != "a"
db.article.find({ "author": { "$ne": "a" }})
```
（11）排序
- 升序：
```
elect * from article where type = "mongodb" order by read desc
db.article.find({"type": "mongodb"}).sort({"read": -1})
```
- 降序
```
select * from article where type = "mongodb" order by read asc
db.article.find({"type": "mongodb"}).sort({"read": 1})
```
- findOne()：除了只返回一个查询结果外，使用方法与find()一样。

2、创建（insert）
```
insert into article(title, author, content) values("mongodb", "tg", "haha")
db.article.insert({"title": "mongodb", "author": "tg", "content": "haha"})
```
3、更新（update）

（1）update()语法：
```
db.collecion.update(query, update[, options] )
```
- query : 必选，查询条件，类似find中的查询条件。
- update : 必选，update的对象和一些更新的操作符（如$,$inc...）等
- options：可选，一些更新配置的对象。
- upsert：可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- multi：可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- writeConcern：可选，抛出异常的级别
简单更新：
```
update article set title = "mongodb" where read > 100
db.article.update({"read": {"$gt": 100}}, {"$set": { "title": "mongodb"}})
```
（2）save()
```
db.article.save({_id: 123, title: "mongodb"})著作权归作者所有。
```
执行上面的语句，如果集合中已经存在一个_id为123的文档，则更新对应字段;否则插入。

注：如果更新对象不存在_id，系统会自动生成并作为新的文档插入。

（3）更新操作符

MongoDB提供一些强大的更新操作符。

更新特定字段（$set）
```
pdate game set count = 10000 where _id = 123
db.game.update({"_id": 123}, { "$set": {"count": 10000}})

删除特定字段（$unset）：
db.game.update({"_id":123}, {"$unset": {"author":1}})
注：$unset指定字段的值只需是任意合法值即可。

递增或递减（$inc）
db.game.update({"_id": 123}, { "$inc": {"count": 10}}) 
注意：$inc对应的字段必须是数字，而且递增或递减的值也必须是数字。
```
数组追加（$push）：
```
db.game.update({"_id": 123}, { "$push": {"score": 123}})
```
还可以一次追加多个元素：

```
db.game.update({"_id": 123}, {"$push": {"score": [12,123]}})
注：追加字段必须是数组。如果数组字段不存在，则自动新增，然后追加。
```
一次追加多个元素（$pushAll）：
```
db.game.update({"_id": 123}, {"$pushAll": {"score": [12,123]}})
```
追加不重复元素（$addToSet）：
$addToSet类似集合Set，只有当这个值不在元素内时才增加：
```
db.game.update({"_id": 123}, {"$addToSet": {"score": 123}})
```
删除元素（$pop）：
```
db.game.update({"_id": 123}, {"$pop": {"score": 1}})  // 删除最后一个元素

db.game.update({"_id": 123}, {"$pop": {"score": -1}})  // 删除第一个元素
```
注：$pop每次只能删除数组中的一个元素，1表示删除最后一个，-1表示删除第一个。著作权归作者所有。

删除特定元素（$pull）：
```
db.game.update({"_id": 123}, {"$pull": {"score": 123}})
```
