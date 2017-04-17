---
title: ssh无密码登录
categories: linux
tags: 
    - ssh
    - linux
---


# ssh无密码登录
## 公钥认证的基本思想
        对信息的加密和解密采用不同的key，这对key分别称作private key和public key，其中，public key存放在欲登录的服务器上，而private key为特定的客户机所持有。
        当客户机向服务器发出建立安全连接的请求时，首先发送自己的public key，如果这个public key是被服务器所允许的，服务器就发送一个经过public key加密的随机数据给客户机，这个数据只能通过private key解密，客户机将解密后的信息发还给服务器，服务器验证正确后即确认客户机是可信任的，从而建立起一条安全的信息通道。
        通过这种方式，客户机不需要向外发送自己的身份标志“private key”即可达到校验的目的，并且private key是不能通过public key反向推断出来的。这避免了网络窃听可能造成的密码泄露。客户机需要小心的保存自己的private key，以免被其他人窃取，一旦这样的事情发生，就需要各服务器更换受信的public key列表。
## 具体实现
- 用ssh-keygen创建公钥
```sh
[root@Server1 ~]# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key(/root/.ssh/id_rsa):
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in/root/.ssh/id_rsa.
Your public key has been saved in/root/.ssh/id_rsa.pub.
The key fingerprint is:
7b:aa:08:a0:99:fc:d9:cc:d8:2e:4b:1a:c0:6b:da:e4root@Server1
The key's randomart image is:
+--[ RSA 2048]----+
| |
| |
| |
|. |
|o. S |
|++. . |
|+=o. . . |
|o+=oB. o |
|..E==*... |
+-----------------+

补充说明
ssh-keygen:生成秘钥
其中：
  -t指定算法
  -f 指定生成秘钥路径
  -N 指定密码
```
- 查看钥匙
```sh
[root@Server1 ~]# ls -l .ssh  
总用量 8  
-rw-------. 1 root root 1675 12月 10 22:20 id_rsa  
-rw-r--r--. 1 root root 394 12月 10 22:20 id_rsa.pub
```
- 将公钥复制到被管理机器Server2和Server3下的.ssh目录下（先确保存在这个目录）
```sh
[root@server1 .ssh]# scp id_rsa.pub root@192.168.1.3:~/.ssh/  
The authenticity of host '192.168.1.3(192.168.1.3)' can't be established.  
RSA key fingerprint is93:eb:f9:47:b1:f6:3f:b4:2e:21:c3:d5:ab:1d:ae:65.  
Are you sure you want to continueconnecting (yes/no)? yes  
Warning: Permanently added '192.168.1.3'(RSA) to the list of known hosts.  
root@192.168.1.3's password:  
id_rsa.pub   
[root@server1 .ssh]# scp id_rsa.pub root@192.168.1.4:~/.ssh/authorized_keys  
The authenticity of host '192.168.1.4(192.168.1.4)' can't be established.  
RSA key fingerprint is93:eb:f9:47:b1:f6:3f:b4:2e:21:c3:d5:ab:1d:ae:65.  
Are you sure you want to continueconnecting (yes/no)? yes  
Warning: Permanently added '192.168.1.4'(RSA) to the list of known hosts.  
root@192.168.1.4's password:  
id_rsa.pub 
```
>到Server2和Server3目录下执行下面的命令
```sh
cat id_rsa.pub >> ~/.ssh/authorized_keys  
```
- 设置文件和目录权限：
```
chmod 600 authorized_keys 
```
- 验证使用SSH IP地址的方式无密码访问
```
[root@server1 .ssh]# ssh 192.168.1.3
```