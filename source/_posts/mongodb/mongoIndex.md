---
title: mongoIndex
date: 2017-04-17 22:53:16
categories: mongodb
tags: 
    - index
    - mongo
---

# 查看索引
```js
> db.user.getIndexSpecs() 
or
> db.user.getIndexes() 
```
# id索引
默认创建 _id
# 单键索引
- 创建索引：
```js
db.user.ensureIndex({x:1})
```
索引可以重复创建，如果索引已存在，则直接返回成功

# 多键索引
创建形式与单键索引相同，区别在于字段的值
单键索引的值为单一的值
多键索引为多个值，例如数组
例：当x有索引时：当插入`> db.user.insert({x:[12,34,55,66]})`,便创建的多键索引

# 复合索引
插入：
```js
> db.user.insert({x:[12,34,55,66]})
WriteResult({ "nInserted" : 1 })
> db.user.ensureIndex({x:1,y:1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 2,
	"numIndexesAfter" : 3,
	"ok" : 1
}
> db.user.getIndexes()
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "blog.user"
	},
	{
		"v" : 2,
		"key" : {
			"x" : 1
		},
		"name" : "x_1",
		"ns" : "blog.user"
	},
	{
		"v" : 2,
		"key" : {
			"x" : 1,
			"y" : 1
		},
		"name" : "x_1_y_1",
		"ns" : "blog.user"
	}
]
>
```
