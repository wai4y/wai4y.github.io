---
title: git记录
date: 2017-11-30 23:54:35
categories:
- git
tags:
- git
---

# git笔记

- `git add readme.txt` `git add -A`
- `git commit -m "comment"`
- `git diff readme.txt`

------

- `git log`  /  `git log --pretty=oneline `
- `git reset --hard HEAD^` 回退到上一个版本  同理 `HEAD^^` 上上个版本
- 在当前窗口没有关闭的情况下  `git reset --hard 87cd3c` 可以回到最新版本
- 如果忘记了commit id 可以使用 `git reflog` 查看之前提交的记录

------

- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。 

<!--more-->

------

**暂存区**   stage -master

撤销更改  

已编辑 `git checkout -- fileName`

 如果已提交到stage  `git reset HEAD fileName`

如果已经commit  第一步 回退版本  `git reset --hard xxx`

创建ssh key  `ssh-keygen -t rsa -C "youremail@example.com"`

` git remote add origin git@github.com:deng55/learngit.git`  deng55 是github的用户名

把本地的仓库关联到github，使用上面的命令

`git push -u origin master` 推送到远程库

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

```
$ git push origin master
```

git clone 有ssh 和http协议 ssh速度快

------

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`





合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。



------

## bug 分支

- git stash  创建stash checkout -b newbranch  回去之前分支，合并，删除新建的分支
- git stash list
- 工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

  一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

  另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

- 可以多次stash  恢复的时候， `git stash apply stash@{0}` 

------

## feature 分支

软件开发中，总有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。



------

查看远程库的信息 `git remote`  `git remote -v`

**推送分支**

`git push origin master`  `git push origin dev`

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

------

## 标签管理

`git tag v1.0`

查看标签 `git tag`

默认标签是打在最新提交的commit上的

```
git tag v0.9 6224937
```

git show <tagname> 查看标签信息，按照字母排序



还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```
$ git tag -a v0.1 -m "version 0.1 released" 3628164
```



还可以通过`-s`用私钥签名一个标签：

```
$ git tag -s v0.2 -m "signed version 0.2 released" fec145a
```

签名采用PGP签名，因此，必须首先安装gpg（GnuPG），如果没有找到gpg，或者没有gpg密钥对，就会报错：

```
gpg: signing failed: secret key not available
error: gpg failed to sign the data
error: unable to sign the tag
```

如果报错，请参考GnuPG帮助文档配置Key。

用命令`git show <tagname>`可以看到PGP签名信息

删除标签  `git tag -d v0.1`

如果要推送某个标签到远程，使用命令`git push origin <tagname>`

一次性推送全部尚未推送到远程的本地标签  `git push origin --tags`

删除远程标签 1.先从本地删除 `git tag -d xx` 2.从远程删除 `git push origin :refs/tags/v0.9`

`ssh-keygen -t rsa -C "[your_email@youremail.com](mailto:your_email@youremail.com)"`

- 忽略某些文件时，需要编写`.gitignore`；
- `.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理！

`git config --global alias.st status` 为status设置别名