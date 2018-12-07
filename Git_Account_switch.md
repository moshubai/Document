# 同一台电脑Git的多账户切换

## 1.创建SSH KEY 
```bash
ssh-keygen -t rsa -C "此处为你自己的邮箱"
```

**！注意！**
> 在执行到
```bash
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):
```
> 的时候不要点击回车，需要重新命名一个秘钥名字如：id_rsa_user,然后一直回车，打开.ssh文件，会看到多出id_rsa_user和id_rsa_user.pub两个文件。


## 2.怎么去调用第二个呢？因为第一个是默认会调用的秘钥。
> 在.ssh文件下生成一个config文件
```bash
touch config 
```
> 需要配置以下内容
```bash
# 该文件是用于配置第一个服务器的秘钥
Host 主机域名
HostName 主机域名
User 用户名
PreferredAuthentications publickey
IdentityFile C:/Users/Administrator/.ssh/id_rsa
 
 # 该文件是用于配置第二个服务器的秘钥
Host 主机域名
HostName 主机域名
User 用户名
PreferredAuthentications publickey
IdentityFile C:/Users/Administrator/.ssh/id_rsa_user


# Host    　　                主机别名
# HostName　　                服务器真实地址
# User　　                    用户名
# PreferredAuthentications　　认证方式
# IdentityFile　　            私钥文件路径
# 上面的配置作用是根据不同的域名选择不同的秘钥。
```
## 3.关键的一步就是：将生成的id_rsa_user.pub文件内容复制在你账户的SSH and GPG keysx中

## 4.检验是否成功
> 打开cmd执行
```bash
ssh -T git@github.com
# 返回是Hi moshubai! You've successfully authenticated, but GitHub does not provide shell access.表示成功
```
```bash
ssh -T git@0.0.0.0
# Welcome to 。。。。。表示成功
```

* end 可能需要改一下你的 name 或者 email
```bash
git config --global user.name "自己的用户名"
git config --global user.email "邮箱地址"
```

如果有哪里不懂的可以先看看，请点击 [《Git基础配置文档》](Git_base.md)。