---
title: multiple github accounts with SSH on Windows
date: 2020-10-07 23:28:21
categories:
- tool
tags:
- SSH
- git

---

### clear old user 

if you have set global git user before, unset it before continue

```shell
git config --global --unset user.email
git config --global --unset user.name
```

### create ssh file

create ssh file with ssh-keygen

run command twice:`ssh-keygen -t rsa -C "xxx@xxx.com"`  then create four file as below:

**remember to set different name of the ssh file**

```shell
accountA # accountA private key
accountA.pub
accountB # accountB private key
accountB.pub
```

### **config file**

this step is very important, under the path in windows, the ssh file located in `C:\Users\your name\.ssh\`, create a new file named with `config` **without any  extension name**,  so the file path is `C:\Users\your name\.ssh\config` and the content in `config` file should be like :

```shell
Host github.com #
	HostName github.com
	User git #this can be anythin you like
	IdentityFile ~/.ssh/accountA # just use the path like this in windows and use the private key

Host github.com-B #add anything you like
	HostName github.com
	User xx
	IdentityFile ~/.ssh/accountB # private key
```

### usage

then you can use git bash with following ways

```shell
# accountA
git clone git@github.com:xx@xx.git

#accountB
git clone git@github.com-B:xx@xx.git
# you should notice the different host, use github.com-B here because we have  the Host in config file above	
```

