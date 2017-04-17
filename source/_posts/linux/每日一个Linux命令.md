---
title: 每日一个Linux命令
categories: linux
tags: 
    - linux
    - 命令
    - bash快捷键
---

## 创建快捷方式
```
ln 源文件 目标文件
  例：ln default /Users/pangmin/Documents/www/nginx-host
ln -s 源文件 目标文件
  例：ln -s /usr/local/etc/nginx/sites-enabled/ 
      /Users/pangmin/Documents/www/site-default
```
## 启动 关闭 重启 nginx
```
开启：nginx
关闭：nginx -s stop
重启：nginx -s reload
```
## 启动 关闭 重启 mysql
```
开启：mysql.server start
关闭：mysql.server stop
重启：mysql.server restart
```
## 启动 关闭 重启 memcache
```
开启：memcached -d
关闭：killall memcached
```
## 启动 关闭 重启 php-fpm
```
开启：php-fpm -D
关闭：killall php-fpm
```
## 安装 卸载 homebrew
```
安装：
ruby -e "$(curl -fsSkL raw.github.com/mxcl/homebrew/go)"
更新
brew update
卸载：
  cd `brew --prefix`
  rm -rf Cellar
  brew prune
  rm `git ls-files`
  rm -r Library/Homebrew Library/Aliases Library/Formula Library/Contributions
  rm -rf .git
  rm -rf ~/Library/Caches/Homebrew
安装软件。
  4.1、查找：brew search
　　如：brew search rar
  4.2、安装， 如：brew install unrar
4.3、卸载， 如：brew uninstall unrar
4.4、查看，如:brew list
```
## 修改权限：
```
例如chmod -R 777 /home/mypackage
```
## 更改用户
```
例 su - pangmin
```
## 查看命令帮助
```
（命令） --help
```
## 查看文件安装路径
```
whereis php
```
## 压缩解压
### zip格式
```shell
zip -r myfile.zip ./*
将当前目录下的所有文件和文件夹全部压缩成myfile.zip文件,－r表示递归压缩子目录下所有文件.

2.unzip
unzip -o -d /home/sunny myfile.zip
把myfile.zip文件解压到 /home/sunny/
-o:不提示的情况下覆盖文件；
-d:-d /home/sunny 指明将文件解压缩到/home/sunny目录下；
3.其他
zip -d myfile.zip smart.txt
删除压缩文件中smart.txt文件
zip -m myfile.zip ./rpm_info.txt
向压缩文件中myfile.zip中添加rpm_info.txt文件
-------------------------------------------------------------------------------
要使用 zip 来压缩文件，在 shell 提示下键入下面的命令：
zip -r filename.zip filesdir
在这个例子里，filename.zip 代表你创建的文件，filesdir 代表你想放置新 zip 文件的目录。-r 选项指定你想递归地（recursively）包括所有包括在 filesdir 目录中的文件。
要抽取 zip 文件的内容，键入以下命令：
unzip filename.zip
你可以使用 zip 命令同时处理多个文件和目录，方法是将它们逐一列出，并用空格间隔：
zip -r filename.zip file1 file2 file3 /usr/work/school 
上面的命令把 file1、file2、 file3、以及 /usr/work/school 目录的内容（假设这个目录存在）压缩起来，然后放入 filename.zip 文件中。
```
### tar格式
```
　压缩
　　tar –cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg
　　tar –czf jpg.tar.gz *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
　　tar –cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
　　tar –cZf jpg.tar.Z *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
　　rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for linux
　　zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux
　　解压
　　tar –xvf file.tar //解压 tar包
　　tar -xzvf file.tar.gz //解压tar.gz
　　tar -xjvf file.tar.bz2 //解压 tar.bz2
　　tar –xZvf file.tar.Z //解压tar.Z
　　unrar e file.rar //解压rar
　　unzip file.zip //解压zip
```
## 将命令输出保存到变量中
-  `符号包含的命令执行完后，可以讲其输出结果保存到变量中
```
v=`java -version`  
echo $v
```

-  还有另一种方法，用$()将命令包起来。
```shell
v=$(java -version)  
echo $v  
```
## bash常用快捷键
>**ctrl+A**
把光标移动到命令行开头。如果我们输入的命令过长,想要把光标移 动到命令行开头时使用。
**ctrl+E**
把光标移动到命令行结尾。
**ctrl+C**
强制终止当前的命令。
**ctrl+L**
清屏,相当于clear命令。
**ctrl+U**
删除或剪切光标之前的命令。我输入了一行很长的命令,不用使用退 格键一个一个字符的删除,使用这个快捷键会更加方便
**ctrl+K**
删除或剪切光标之后的内容。
**ctrl+Y**
粘贴ctrl+U或ctrl+K剪切的内容。
**ctrl+R**
在历史命令中搜索,按下ctrl+R之后,就会出现搜索界面,只要输入 搜索内容,就会从历史命令中搜索。
**ctrl+D**
退出当前终端。
**ctrl+Z**
暂停,并放入后台。这个快捷键牵扯工作管理的内容,我们在系统管 理章节详细介绍。
**ctrl+S**
暂停屏幕输出。
**ctrl+Q**
恢复屏幕输出。

## hash
- hash：列出
- hash -d COMMOND:删除
- hash -r 清空

## 变量
- 程序：指令+数据
    - 指令：有程序文件提供
    - 数据：io设备，文件，管道，变量

## 查看Linux版本
- uname -a
- cat /proc/version

## apt-get
- `-y`参数
安装遇到` Do you want to continue? [Y/n] Abort`时，使用这个参数自动选择Y

## history
- 在历史中搜索：快捷键`Ctrl + R`
- 清空历史命令：`history -c`

## ls
- l:
```
drwxrwxrwx 2 chimps chimps 4096 2月   1 03:16 文档
drwxrwxrwx 2 chimps chimps 4096 3月  13 20:27 下载
drwxrwxrwx 2 chimps chimps 4096 2月   1 03:16 音乐
drwxrwxrwx 2 chimps chimps 4096 2月   1 03:16 桌面
```
    - 第一个首字母表示文件类型：`d`：directory，`-`：普通文件，`l`:link
    - 第二个表示文件连接数。
- S：order by size
- k：显示大小以k字节为单位
- h：以更直观的方式查看文件大小