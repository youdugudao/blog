---
title: CRUD操作
categories: mongodb
tags: 
    - database
    - auth
---

# insert
- 正常插入：
```
> db.user.insert({x:1,y:2,z:3})
WriteResult({ "nInserted" : 1 })
```
- 循环插入
```
> for(var i=1;i<100;i++) db.user.insert({x:i, y:i*10, z:i*100})
WriteResult({ "nInserted" : 1 })
```
# update
- 整个更新
```
> db.user.find()
{ "_id" : ObjectId("58d0d844bae387d0c1e8d870"), "x" : 1, "y" : 2 }
{ "_id" : ObjectId("58d0d84ebae387d0c1e8d871"), "x" : 2, "y" : 3 }
> db.user.update({x:1},{x:999})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.user.find()
{ "_id" : ObjectId("58d0d844bae387d0c1e8d870"), "x" : 999 }
{ "_id" : ObjectId("58d0d84ebae387d0c1e8d871"), "x" : 2, "y" : 3 }
```
- 部分更新
使用$set操作符
```
> db.user.find()
{ "_id" : ObjectId("58d0d844bae387d0c1e8d870"), "x" : 999 }
{ "_id" : ObjectId("58d0d84ebae387d0c1e8d871"), "x" : 2, "y" : 3 }
> db.user.update({x:2},{$set:{x:999}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.user.find()
{ "_id" : ObjectId("58d0d844bae387d0c1e8d870"), "x" : 999 }
{ "_id" : ObjectId("58d0d84ebae387d0c1e8d871"), "x" : 999, "y" : 3 }
```
- 不存在则创建
```
> db.user.find({x:1})
> db.user.update({x:1},{x:2},true)
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 1,
	"nModified" : 0,
	"_id" : ObjectId("58d0da6e34123eac45d857ac")
})
> db.user.find()
{ "_id" : ObjectId("58d0d844bae387d0c1e8d870"), "x" : 999 }
{ "_id" : ObjectId("58d0d84ebae387d0c1e8d871"), "x" : 999, "y" : 3 }
{ "_id" : ObjectId("58d0da6e34123eac45d857ac"), "x" : 2 }
```
- 更新多条
mongodb默认是更新一条，据说是为了防止一时大意，
而且更新多条不允许更新整个，只能使用$set关键字
```
> db.user.find()
{ "_id" : ObjectId("58d0dde9bae387d0c1e8d873"), "x" : 1, "y" : 2, "z" : 1 }
{ "_id" : ObjectId("58d0ddeabae387d0c1e8d874"), "x" : 1, "y" : 1, "z" : 1 }
{ "_id" : ObjectId("58d0ddebbae387d0c1e8d875"), "x" : 1, "y" : 1, "z" : 1 }
> db.user.update({x:1},{$set:{y:2}},false,true)
WriteResult({ "nMatched" : 3, "nUpserted" : 0, "nModified" : 2 })
> db.user.find()
{ "_id" : ObjectId("58d0dde9bae387d0c1e8d873"), "x" : 1, "y" : 2, "z" : 1 }
{ "_id" : ObjectId("58d0ddeabae387d0c1e8d874"), "x" : 1, "y" : 2, "z" : 1 }
{ "_id" : ObjectId("58d0ddebbae387d0c1e8d875"), "x" : 1, "y" : 2, "z" : 1 }
```
#  remove
- 删除某个collection所有内容
```
> db.user.remove({})
WriteResult({ "nRemoved" : 102 })
```
- 删除某个
```
> db.user.remove({x:3})
WriteResult({ "nRemoved" : 1 })
```
#  find
- 查找所有
```
> db.user.find()
{ "_id" : ObjectId("58d0cebcbae387d0c1e8d80a"), "username" : "zhangsan" }
{ "_id" : ObjectId("58d0d36abae387d0c1e8d80b"), "x" : 1, "y" : 2, "z" : 3 }
{ "_id" : ObjectId("58d0d37dbae387d0c1e8d80c"), "x" : 1, "y" : 2, "z" : 3 }
```
- 有条件查找
```
> db.user.find({x:1})
{ "_id" : ObjectId("58d0d36abae387d0c1e8d80b"), "x" : 1, "y" : 2, "z" : 3 }
{ "_id" : ObjectId("58d0d37dbae387d0c1e8d80c"), "x" : 1, "y" : 2, "z" : 3 }
```
- 查找总数目
```
> db.user.find().count()
102
```
- 分页,排序
```
> db.user.find().skip(5).limit(3).sort({x:1})
{ "_id" : ObjectId("58d0d453bae387d0c1e8d80f"), "x" : 3, "y" : 30, "z" : 300 }
{ "_id" : ObjectId("58d0d453bae387d0c1e8d810"), "x" : 4, "y" : 40, "z" : 400 }
{ "_id" : ObjectId("58d0d453bae387d0c1e8d811"), "x" : 5, "y" : 50, "z" : 500 }
```
