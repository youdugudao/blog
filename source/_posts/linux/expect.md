---
title: expect交互编程
categories: linux
tags: 
    - linux
    - expect
---

# 安装
```
Ubuntu：apt-get install expect
mac: brew install homebrew/dupes/expect
```
# 设置变量
```
set password  rootddd
```
# 获取参数
```
set filename [lindex $argv 0]
```
# 一般格式
> 开头一定要是expect
```
#!/usr/bin/expect

set filename [lindex $argv 0]
set password  example_pass
# 开始执行命令
spawn ssh root@example.com
# 匹配（如果包含，则执行下面的send）
expect "password:"
# \r回车
send "$password\r"
send "ls\r"
send "exit\r"
# 结束标志
interact
```