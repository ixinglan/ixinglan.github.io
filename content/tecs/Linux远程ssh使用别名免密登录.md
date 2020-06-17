---
title: "Linux 使用别名免密ssh连接"
date: 2020-06-17T00:02:17+08:00
draft: false
---
# 服务器ip地址
假设有3台服务器server1,server2,server3, IP地址信息如下
* server1 101.200.1.1(公网) 192.168.1.1(内网)
* server2 201.200.1.1(公网) 192.168.1.2(内网)
* server3 301.200.1.1(公网) 192.168.1.3(内网)  
我们假设3台服务器属于同一个内网(当然可以通过公网ip配置),接下来,我们配置服务器之间的别名免密登录
# 配置别名
未配置之前我们通过ssh连接远程服务器的命令是这样的
```shell script
ssh user@host #然后按提示输入密码
```
在个人用户目录下进入.ssh隐藏目录,如果没有config配置文件的话,使用touch config创建一个
```
vim config #打开配置文件, 配置如下信息

Host server1 #别名
HostName 192.168.1.1 #ip地址,我们这里使用内网ip
User root #目标服务器用户名,我们这里使用root用户
IdentitiesOnly yes #固定写法

Host server2 
HostName 192.168.1.2
User root 
IdentitiesOnly yes 

Host server3 
HostName 192.168.1.3
User root 
IdentitiesOnly yes 
```
这时候我们只需要 ssh server1这样就可以连接了,省去了输入用户名和ip地址,但这时候仍然会提示输入密码,这是我们不好记忆的  
# 配置免密登录  
## 在每台服务器下生成rsa 公钥和私钥
```shell script
ssh-keygen -t rsa #生成密钥命令,然后一路enter键就行

Generating public/private rsa key pair.
Enter file in which to save the key (/Users/zhaojianqiang/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/zhaojianqiang/.ssh/id_rsa.
Your public key has been saved in /Users/zhaojianqiang/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:Ku1uTYb5lc6QhompAmYJUEF7LBvFD9gzJ18Ax69J4I8 zhaojianqiang@localhost
The key's randomart image is:
+---[RSA 3072]----+
| o+=ooo.         |
|. .+O.o .        |
|. +.oX o         |
|.  =. + .        |
|. o  * BS. .     |
|.+  E.O.* o      |
|+  .. o* =       |
|. .  o. o o      |
| .   oo          |
+----[SHA256]-----+
```
成功后会在.shh目录下生成id_rsa(私钥)  id_rsa.pub(公钥)两个文件  
## 将公钥拷贝到目标服务器
这个过程实际是一个信任过程,比如将server1的id_rsa.pub拷贝到server2,server3,则代表server2,server3允许server1免密ssh连接,同理
将server2的公钥拷贝到server1,server3, 将server3的公钥拷贝到server1,server2  
拷贝方式有两种,例如  
1.例如在server1下执行以下命令
```shell script
ssh-copy-id sever2 #这时候仍需输入密码,注意:因为我们配置了别名,所以直接使用别名,如未配置,则需要ssh-copy-id username@host

#我们会发现这个命令的结果是将server1的公钥拷贝在了server2 的.ssh目录下的authorized_keys配置文件里
```
2.手动copy,例如在server1下执行以下命令
```shell script
cat id_rsa.pub #打开公钥文件,将内容复制一下
ssh server2 #连接server2
cd .ssh
vim authorized_keys #打开 authorized_keys配置文件
#将刚才复制的公钥内容写入该authorized_keys文件保存即可, 第2种方式实际同第1种一样,手动操作更繁琐一些
```  
## 免密连接
至此,我们就可以远程ssh免官连接了,例如,在server1下
```shell script
ssh server2 #直接进入了server2的用户目录,无需输入密码
```
# mac终端如果免密连接上述服务器
由于个人笔记本与上述server1,server2,server3不在同一个内网,帮config配置文件需要配置server1,server2,server3的公网ip  
mac上生成本地私钥和公钥文件  
将生成的公钥文件内容copy到server1,server2,server3的authorized_keys配置文件,流程和上面一样  
至此,就可以畅快的免密连接你的个人服务器了,无需输入ip和密码, 只需记住简单的别名即可