---
title: auth认证
categories: mongodb
tags: 
    - database
    - auth
---

# 以auth认证方式启动
- 可以在config文件中加一项：
```
auth: true
```
- 可以在命令行中，添加一个option，`--auth`

但是，如果mongodb中没有用户，认证是没有意义的。
# 创建用户
- 选择库
```
// 用户的存储也是基于库的。管理员用户的话，需要在admin中创建。创建的第一个用户即为管理员用户
> use admin
```
- 创建管理员用户
```
db.createUser({
    user:"root", 
    pwd:"root", 
    roles:[
     	{
            role:"userAdminAnyDatabase", 
            db:"admin"
        }
    ]
})
```
- 创建普通用户
```
db.createUser({
    user:"user1", 
    pwd:"pwd1", 
    roles:[
     	{
            role:"readWrite", 
            db:"db1"
        }
    ]
})
```

# 登录
```
// user所存储的库
> use admin
> db.auth(username, password)
// 返回原数据库
> use blog
```
