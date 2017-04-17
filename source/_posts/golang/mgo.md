---
title: mgo
categories: golang
---

>mongodb客户端的golang实现
mgo很有可能作为golang的标准库
[mgo文档](https://godoc.org/gopkg.in/mgo.v2)

# 连接
```go
package database

import (
	"full_text_search/config"
	"gopkg.in/mgo.v2"
)

var Session *mgo.Session

// 简单实现单例，只创建一个session
func init() {
	if Session == nil {
		var err error
		Session, err = mgo.Dial(config.MongoDB.Host + ":" + config.MongoDB.Port)
		if err != nil {
			panic("连接mongodb失败，原因：" + err.Error())
		}
	}
}

func Collection(collectionName string, f func(collection *mgo.Collection)) (err error) {
    // mgo内部实现了连接池，通过clone获取连接
	session := Session.Clone()
	defer func() {
		session.Close()
		if err := recover(); err != nil {
			return
		}
	}()
	collection := session.DB(config.MongoDB.Database).C(collectionName)
	f(collection)
	return
}
```
# 登录
```go
Session, err = mgo.Dial(config.MongoDB.Host + ":" + config.MongoDB.Port)
if err != nil {
	panic("连接mongodb失败，原因：" + err.Error())
}
err = Session.DB("admin").Login(config.MongoDB.User, config.MongoDB.Password)
if err != nil{
	panic("登录mongodb失败，原因：" + err.Error())
}
```
# 增删改查
上述是建立连接，并执行操作
## insert
```go
type d struct {
	X int
	Y int
	Z int
}
database.Collection("user", func(collection *mgo.Collection) {
	var insertDatas = []interface{}{
		d{6, 6, 6},
		d{7, 6, 6},
		d{8, 6, 6},
	}
	var insertData = d{9, 9, 9}
	//插入单条
	collection.Insert(insertData)
	//插入多条
	collection.Insert(insertDatas...)
})
```
## select
```go
type d struct {
	X int
	Y int
	Z int
}

// 为了代码的可读性，没有验证错误error
database.Collection("user", func(collection *mgo.Collection) {
	var resDatas []d
	var resData d
	query := collection.Find(bson.M{"x": 1})
	//统计数目
	count, _ := query.Count()
	//链式操作，类似函数式编程
	query = query.Sort("y").Skip(1).Limit(2)
	//查询所有
	query.All(&resDatas)
	//查询第一个
	query.One(&resData)
	fmt.Println(resDatas, resData, count)
})
```
## delete
```go
// 为了代码的可读性，没有验证错误error
database.Collection("user", func(collection *mgo.Collection) {
	collection.Remove(bson.M{"x": 1})
})
```
## update
- 修改第一条，修改此条全部内容
```go
//struct 的键需要大写
collection.Update(bson.M{"x": 3}, struct {
	X int
	Y int
	Z int
}{3, 4, 5})
```
- 修改第一条，修改此条部分内容
```go
collection.Update(bson.M{"x": 0}, bson.M{"$set": bson.M{"y": 1}})
```
- 修改多条，只能修改每一条的部分内容
```go
changeLog, err := collection.UpdateAll(bson.M{"x": 6}, bson.M{"$set": bson.M{"y": 1}})
if err != nil {
	fmt.Println(err.Error())
}
fmt.Println("Matched:", changeLog.Matched)
fmt.Println("Removed:", changeLog.Removed)
fmt.Println("Updated:", changeLog.Updated)
fmt.Println("UpsertedId:", changeLog.UpsertedId)
// 结果
> go run test.go
Matched: 2
Removed: 0
Updated: 2
UpsertedId: <nil>
```