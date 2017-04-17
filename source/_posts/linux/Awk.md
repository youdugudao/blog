---
title: Awk
categories: linux
tags: 
    - linux
    - awk
---

>参见[linux手册awk部分](http://man.linuxde.net/awk)
# 示例文本
test.txt
```
Beth	4.00	0
Dan	3.75	0
kathy	4.00	10
Mark	5.00	20
Mary	5.50	22
Susie	4.25	18
```
# 基本语法示例
`awk '$3 >0 { print $1, $2 * $3 }' emp.data`

# 通过awk脚本文件
test.awk
```
/.+th/ {
    print $0
}
```
运行脚本：
```
$ awk -f test.awk test.txt
# 结果：
Beth    4.00    0
kathy   4.00    10
```
# BEGIN与END
```
BEGIN{
    print "begin search"
}
END{
    print "all end"
}
/.+th/{
    print $0
}
```
运行：
```
$ awk -f test.awk test.txt
# 结果
begin search
Beth    4.00    0
kathy   4.00    10
all end
```

# 内置变量
## ARGC
表示在命令中提供的参数的个数
例：
```
awk 'BEGIN {print "Arguments =", ARGC}' One Two Three Four
```
结果：`Arguments = 5`
## ARGV
这个变量表示存储命令行输入参数的数组,数组的有效索引是从 0 到 ARGC-1
例
```
BEGIN{
    for (i = 0; i < ARGC ;i++){  
        printf "ARGV[%d]=%s\n", i, ARGV[i]
    }
}
END{
    print "all end"
}
/.+th/{
    print $0
}
```
运行：
```
$ awk -f test.awk test.txt
# 结果
ARGV[0]=awk
ARGV[1]=test.txt
Beth    4.00    0
kathy   4.00    10
all end
```
## ENVISON环境变量
```
BEGIN{
    for (n in ENVIRON){
        print n ": " ENVIRON[n]
    }
}
```
运行：
```
$ awk -f test.awk test.txt
# 结果
GOROOT: /usr/local/Cellar/go/1.7.4_1/libexec
LESS: -R
PAGER: less
AWKPATH: .:/usr/local/Cellar/gawk/4.1.4_1/share/awk
OLDPWD: ...
TERM_PROGRAM: iTerm.app
```
## FILENAME
文件名
```
BEGIN{
    print FILENAME
}
END{
    print FILENAME
}
```
运行：
```
/chimps/linux/awk awk -f test.awk test.txt

test.txt
```
可以看出在begin的时候FILENAME变量为空值
## FS
此变量表示输入的数据域之间的分隔符，其默认值是空格。 你可以使用 -F 命令行选项改变它的默认值。
## NF
此变量表示当前输入记录中域的数量
test.txt
```
Marks
Mark	5.00
Mary	5.50	22
Susie	4.25	18
```
test.awk
```
NF>2{
    print $0
}
```
执行：
```
/chimps/linux/awk awk -f test.awk test.txt
Mary	5.50	22
Susie	4.25	18
```
## NR（FNR）
默认的就是读取的数据行数，NR可以理解为Number of Record的缩写,FNR则为File Number of Record
```
# class1:
zhaoyun 85 87
guanyu 87 88
liubei 90 86
# class2:
caocao 92 87 90
guojia 99 96 92
# test.awk:
{
    print NR,FNR,$0
}
```
执行：
```
/chimps/linux/awk awk -f test.awk class1 class2
1 1 zhaoyun 85 87
2 2 guanyu 87 88
3 3 liubei 90 86
4 1 caocao 92 87 90
5 2 guojia 99 96 92
```

# 数组操作
```
BEGIN{
    arr[1] = "zhangsan"
    arr["lisi"] = "lisi"
    arr[2] = "wangwu"

    for (n in arr){
        print arr[n]
    }
}
```
运行：
```
/chimps/linux/awk awk -f test.awk test.txt
lisi
zhangsan
wangwu
```
## 判断数组中是否存在某一项
```
BEGIN{
    arr["zhangsan"] = "wangwu"
    if("zhangsan" in arr){
        print "zhangsan ok"
    }else{
        print "zhangsan not found"
    }

    if("lisi" in arr){
        print "lisi ok"
    }else{
        print "lisi not found"
    }
}
```
运行：
```
/chimps/linux/awk awk -f deal_err.awk
zhangsan ok
lisi not found
```

# DEMO
## 获取每个进程的内存占用
test.awk:
```
NR != 1 && $4 > 0{
    mem[$4] = $11
}
END{
    asorti(mem, Nmem)
    for (k in Nmem){
        print mem[Nmem[k]], Nmem[k]
    }
}
```
运行：
`ps aux | awk -f test.awk`
## 多行日志处理
file.log：
```
[2017-02-17 16:19:04] [285]
<- /v2/api/file/item/476
-> {"success":true,"successInfo":{}}

[2017-03-10 11:57:42] []
<- /v2/api/file/diff?fid1=381&fid2=287
-> {"success":false,"errorInfo":"长度不能为空","successInfo":{},"debug":{"line":37,"info":null}}
...
```
### 方式一：使用数组
deal_err.awk
```
BEGIN{
    sub_index = 0
}
/^\[/{
    arr_line[sub_index] = $1 "\t" $2
}

/^<-/{
    arr_line[sub_index] = arr_line[sub_index] "\t" $2
}

/^->/{
    text = substr($2, 2, length($2)-2)
    split(text, text_arr, ",")
    split(text_arr[1], text_arr1, ":")
    if (text_arr1[2] == "false"){
        arr[sub_index] = arr_line[sub_index] "\t" text_arr[2]
        sub_index ++
    }
}

END{
    for (k in arr) {
        print k,arr[k] >> "output.txt"
    }
}
```
运行：
```
awk -f deal_err.awk file.log
```

### 方式二：匹配成功就写到文件
deal_err.awk
```
/^\[/{
    url = $1 "\t" $2
}

/^<-/{
    request = arr_line[sub_index] "\t" $2
}

/^->/{
    response = substr($2, 2, length($2)-2)
    split(response, res_arr, ",")
    split(res_arr[1], res_arr1, ":")
    if (res_arr1[2] == "false"){
        print url,request,res_arr[2] > "output.txt"
    }
}
```
很明显，这样可读性好一些
运行：
```
awk -f deal_err.awk file.log
```

