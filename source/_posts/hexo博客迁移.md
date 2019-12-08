---
title: hexo博客迁移
date: 2019-12-08 12:59:28
tags: blog
categories:
- 2019
- 12月
---

使用github pages 创建个人博客和方便，但是在更换电脑后，怎么把原有的文件找回是个问题，最近重新把这个博客找回来，也算做个记录。

### 本地文件同步到Github

**针对已经创建发布过的博客，历史文件还在旧电脑上，**按顺序执行以下步骤进行迁移。

<!--more-->

1. github pages的静态文件是放在`username.github.io`这个仓库下的master分支的，所以我们可以新建一个branch，命名为`hexo`，并把这个分支设置为**默认分支**，

​     {% asset_img hexo_migration.png image %}

2. 本地电脑上clone刚才创建的这个分支（这里我们假设为**new**），并把clone下来的分支里面**除了.git文件夹**之外的东西都删除。

3. 把之前电脑上的博客本地文件夹（假设为**old**）的文件都复制到新建的**new**文件夹下，这一步很关键，**old**里面有几个文件夹不要复制，`.deploy_git`,`.git`,`node_modules`,这三个**old**里面的文件夹不要复制到**new**里面

4. 如果之前有安装过（clone方式）其他主题，你在`theme`文件夹下应该可以看到对应的文件夹，比如我安装的就是`next`主题，进入`theme/next`文件夹，把里面的`.git`文件夹删除，如果是用默认的主题，这一步可以忽略。

5. 执行以下命令，把**new**文件夹上传到github

   ```git
   git add .
   git commit -m "comment"
   git push
   ```

   执行完以上命令后，就可以去github的hexo分支看到我们刚才复制的文件了。这样其实是把我们的配置和文件之类的做了一个备份，以后每次新建文章，记得都要上传到github。

### 本地hexo的安装

现在来进行本地的hexo安装， 上面的功能只是把我们之前的文档还原了，但是还不能写文章。

假设电脑上没有安装过hexo，执行以下命令安装，npm和node都要提前安装好, 下面的命令**一定不要执行`hexo init` **

```shell
1. npm install hexo-cli -g  # 这里是安装hexo
2. npm install # 这一步要进入到你之前创建的new文件夹下操作，执行命令会重新创建node_modules文件夹
3. npm install hexo-deployer-git --save # 安装hexo的git部署工具
```

如果电脑上已经安装过hexo， 只需要从第二条命令开始执行。

接下来就可以使用`hexo new post <title>`来写文章了，剩下的其他命令可以直接参考hexo官方文档了。

>  https://hexo.io/zh-cn/docs/writing 

```shell
hexo g -d #写完文章后，执行这个命令可以生成静态文件并部署网站
hexo clean # 如果发布文章一直不生效，可以先执行这个命令，在执行上面的第一个命令
```

