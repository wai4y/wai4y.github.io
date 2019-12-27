---
title: tlcl笔记
date: 2017-12-11 23:29:50
categories:
- Linux
tags:
- linux
---

## 日常命令

- ls List  directory contents

 ` ls ~  /usr  列出用户家目录`` ls -l  结果以长模式输出`

- file Determine file type
- less view file contents

## 命令参数 command -option arguments

eg: 
`ls -lt` l选项产生长格式输出，t选项按文件修改时间的先后顺序

| 选项   | 长选项              | 描述                           |
| ---- | ---------------- | ---------------------------- |
| -a   | --all            | 列出所有文件                       |
| -d   | --directory      | 把这个选项与-l结合使用，可以看到所指定目录的详细信息  |
| -F   | --classify       | 会在每个所列出的名字后面加上一个指示符‘/’       |
| -h   | --human-readable | 当以长格式列出时，以可读格式显示             |
| -l   | --               | 以长格式显示                       |
| -r   | --reverse        | 以相反的顺序来显示结果，通常，ls命令的结果按照升序排列 |
| -S   |                  | 命令输出结果安装文件大小排序               |
| -t   |                  | 按照修改时间来排序                    |

长格式输出范例：

```
-rw-r--r-- 1 root root 3576296 2007-04-03 11:05 Experience ubuntu.ogg
-rw-r--r-- 1 root root 1186219 2007-04-03 11:05 kubuntu-leaflet.png
-rw-r--r-- 1 root root 47584 2007-04-03 11:05 logo-Edubuntu.png
-rw-r--r-- 1 root root 44355 2007-04-03 11:05 logo-Kubuntu.png
-rw-r--r-- 1 root root 34391 2007-04-03 11:05 logo-Ubuntu.png
-rw-r--r-- 1 root root 32059 2007-04-03 11:05 oo-cd-cover.odf
-rw-r--r-- 1 root root 159744 2007-04-03 11:05 oo-derivatives.doc
-rw-r--r-- 1 root root 27837 2007-04-03 11:05 oo-maxwell.odt
-rw-r--r-- 1 root root 98816 2007-04-03 11:05 oo-trig.xls
|访问权限 || 硬链接数目 || 文件属主的用户名 || 文件所属用户组的名字 || 以字节数表示的文件大小 || 上次修改文件的时间和日期 || 文件名`
```

<!--more-->

## 确定文件类型

`file filename` 打印brief description of the file contents

### 用`less`浏览文件内容

`less filename`

| 命令                 | 行为                           |
| ------------------ | ---------------------------- |
| Page UP or b       | 向上翻滚一页                       |
| Page Down or space | 向下翻滚一页                       |
| UP Arrow           | 向上翻滚一行                       |
| Down Arrow         | 向下翻滚一行                       |
| G                  | 移动到最后一行                      |
| 1G or g            | 移动到开头一行                      |
| /charaters         | 向前查找指定的字符串                   |
| n                  | 向前查找下一个出现的字符串，这个字符串是之前所指定查找的 |
| h                  | 显示帮助屏幕                       |
| q                  | 退出 less 程序                   |

**less属于页面调度器程序类**

### 目录结构

`/etc` 系统配置文件 
`/lib` 类似windows的dll文件 
`/opt` 用来安装“optional”的软件 
`/root` 是root账户的home目录 
`/sbin` 可以理解为super user bin 
`/tmp` 临时文件，有些临时文件会在重启的时候清空

`/usr` 可能是linux系统下最大的一个目录，包含着普通普通用户所需要的所有程序和文件 
`/usr/bin` 包含系统安装的可执行文件，通常，这个目录会包含许多程序 
`/usr/lib` 包含由`/usr/bin`目录所用的共享库 
`/usr/local` 这个/usr/local 目录，是非系统发行版自带，却打算让系统 
​           使用的程序的安装目录。通常，由源码编译的程序会安装 
​           在/usr/local/bin 目录下。新安装的 Linux 系统中，会存在 
​           这个目录，但却是空目录，直到系统管理员放些东西到它里 
​           面。 
`/usr/share` 包含许多由/usr/bin 目录中的程序使用的共 
享数据。其中包括像默认的配置文件，图标，桌面背景，音 
频文件等等 
`/usr/share/doc` 文档类 
`/var` 除了`/tmp`和`/home`外，目前我们看到的目录是静态的，`/var`目录是可能需要改动的文件存储的地方，各种数据库，假脱机文件，用户邮件等，都在这里。 
`/var/log`日志文件 
完整目录解析：<http://www.pathname.com/fhs/>

## 操作文件和目录

 `cp 复制文件和目录``mv 移动/重命名文件和目录``mkdir 创建目录``rm 删除文件和目录``ln 创建硬链接和符号链接`

以上为最常用的linux命令 
**为什么要使用命令行？**

> 因为图形界面不能处理一些比较负责的任务，比如复制当前目录下所有文件到指定目录，同时不能复制目标文件夹已经存在的文件以及版本大于当前版本的？使用命令行可以解决 
>   `cp -u *.html destination`

### 通配符

| 通配符             | 意义                    |
| --------------- | --------------------- |
| `*`             | 匹配任意多个字符（包括零个或一个）     |
| `?`             | 匹配任意一个字符（不包括零个）       |
| `[characters]`  | 匹配任意一个属于字符集中的字符       |
| `[!characters]` | 匹配任意一个不是字符集中的字符       |
| `[[:class:]]`   | 匹配任意一个属于指定**字符类**中的字符 |

常用的**字符类**

| 字符类         | 意义          |
| ----------- | ----------- |
| `[:alnum:]` | 匹配任意一个字母或数字 |
| `[:alpha:]` | 匹配任意一个字母    |
| `[:digit:]` | 匹配任意一个数字    |
| `[:lower:]` | 匹配任意一个小写字母  |
| `[:upper]`  | 匹配任意一个大写字母  |

使用通配符能选择非常复杂的文件名

| 模式                     | 匹配对象                                |
| ---------------------- | ----------------------------------- |
| *                      | 所有文件                                |
| g*                     | 文件名以`g`开头                           |
| b*.txt                 | 以`b` 开头，中间有零个或任意多个字符，并以`.txt` 结尾的文件 |
| Data???                | 以`Data`开头，其后紧接着 3 个字符的文件            |
| [abc]*                 | 文件名以`a,b,或c` 开头的文件                  |
| BACKUP.[0-9][0-9][0-9] | 以`BACKUP.` 开头，并紧接着 3 个数字的文件         |
| [[:upper:]]*           | 以大写字母开头的文件                          |
| [![:digit:]]*          | 不以数字开头的文件                           |
| *[[:lower:]123]        | 文件名以小写字母结尾，或以“1”，“2”，或“3”结尾的文件      |

**接收文件名作为参数的任何命令，都可以使用通配符** 
gui中也可以使用通配符 如在Nautilus中通过Edit/Select模式菜单项选中文件，输入一个用通配符表示的文件选择模式后，那么当前所浏览的目录中，所匹配的文件名就会高亮显示

### 创建目录

#### `mkdir directory...`

如上，如果描述一个命令，后面有三个点，表示这个参数可以重复 
`mkdir dir1 dir2 dir3`

### 复制文件和目录

有两种方法实现

- `cp item1 item2` 复制单个文件或目录`item1`到`item2`
- `cp item... directory` 复制多个项目（文件或目录）到一个目录下

#### cp命令常用的选项

| 命令               | 含义                                       |
| ---------------- | ---------------------------------------- |
| -a,--archive     | 复制文件和目录，以及他们的属性，包括所有权和权限，通常，复本具有用户所操作文件的默认属性 |
| -i,--interactive | 在重写已经存在的文件之前，提示用户确认，如果不指定，默认重写           |
| -r,--recursive   | 递归的复制目录和目录中的内容。当复制目录时需要这个选项（或者使用-a选项）    |
| -u,--update      | 当把文件从一个目录复制到另一个目录时，仅复制目标目录中不存在的文件，或者是文件内容新于目标目录中已经存在的文件 |
| -v,--verbose     | 显示详细的命令操作信息                              |

| 命令                  | 运行结果                                     |
| ------------------- | ---------------------------------------- |
| cp file1 file2      | 复制file1到file2，如果file2存在，用file1重写file2    |
| cp -i file1 file2   | 同上一条命令，只是如果file2存在，会让用户确认是否重写            |
| cp file1 file2 dir1 | 复制file1和file2到目录dir1，dir1必须已经存在          |
| cp dir1/* dir2      | 使用通配符，把dir1中的所有文件都复制到dir2，dir2必须已经存在     |
| cp -r dir1 dir2     | 复制目录 dir1 中的内容到目录 dir2。如果目录 dir2 不存在，创建目录 dir2，操作完成后，目录 dir2 中的内容和 dir1 中的一样。如果目录 dir2 存在，则目录 dir1 (和目录中的内容) 将会被复制到 dir2 中 |

### `mv`移动和重命名文件

与cp命令比较像 
`mv item1 item2` 把文件或目录`item1`移动或重命名为`item2` 
`mv item... directory` 把一个或多个条目移到到另一个目录中

#### `mv`命令的选项

 `mv和cp有类似的命令选项``-i,--interactive``-u,--update``-v,--verbose````mv file1 file2``mv -i file1 file2``mv file1 file2 dir1``mv dir1 dir2`

### `rm`-删除文件和目录

`rm itme...` item代表一个或多个文件或目录

#### 命令选项

 `-i , --interactive``-r , --recursive``-f , --force 忽视不存在的文件，不显示文件信息``-v , --verbose````rm file1 静默删除``rm -i file1 删除前的提示``rm -r file1 dir1 删除file1和目录dir1及其文件``rm -rf file1 dir1 如果文件和目录不存在，rm仍会静默执行`

**小心使用rm命令，rm没有复原命令，注意通配符的使用， 
 无论什么时候，rm 命令用到通配符（除了仔细检查输入的内容外！），用 ls 命令来测试通配符。这会让你看到要删除的文件列表。然后按下上箭头按键，重新调用刚刚执行的命令，用 rm 替换 ls**

### `ln`创建链接

`ln file link` 
to create a hard link 创建硬链接 
`ln -s item link` 
to create a symbolic link 创建符号链接，item可以是一个文件或是一个目录

#### 硬链接

- 一个硬链接不能关联他所在文件系统之外的文件，一个链接不能关联与链接本身不在同一个磁盘分区上的文件
- 一个硬链接不能关联一个目录

#### 软链接

 也称符号链接，通过一个特殊类型的文件，这个文件包含一个关联文件或目录的文本指针，类似于windows快捷方式

`cp /etc/passwd .`复制目录文件到当前工作目录

 创建符号链接的目的是为了克服硬链接的两个缺点：硬链接不能跨越物理设备，硬链接不能关联目录，只能是文件

## 使用命令

```
type--说明怎样解释一个命令名
which--显示会执行哪个可执行程序
man--显示命令手册页display a  command's manual page 
apropos--display a list of appropriate commands显示适合的命令info--display command's info entry 显示命令info
whatis -- display a brief description of a command
alias-- 创建命令别名
```

### 什么是命令

命令的四种形式：

- 可执行程序，`/usr/bin`目录中的文件。属于这一类的程序，可以编译成二进制文件
- 一个内建于shell自身的命令，叫做shell内部命令，例如cd
- 是一个shell函数，这些是小规模的shell脚本，他们混合到环境变量中，
- 是一个命令别名，

### 识别命令

#### `type`显示命令的类型

`type`是shell内部命令，显示命令的类别，`type command`

#### `which`-显示一个可执行程序的位置

`which ls` 这个命令只对可执行程序有效，不包括内部命令和命令别名，别名是真正的可执行程序的替代物

### `help`-帮助命令

`help cd` 
`mkdir --help`

### 显示程序手册页 `man`

`man program`

### `apropos` -- 显示适当的命令

`apropos floppy`

### `whatis`--显示非常简洁的说明

`whatis`显示匹配特定关键字的手册页的名字和一行命令说明

### 'info'--显示程序info条目

info页是超链接形式的，和网页很相似

### Readme和其他文档

位于`/usr/share/doc`目录下 
我们可能遇到许多以 “.gz” 结尾的文件。这表示 gzip 压缩程序已 
经压缩了这些程序。gzip 软件包包括一个特殊的 less 版本,叫做 zless,zless 可以显示由 gzip 
压缩的文本文件的内容。

### 使用`alias`创建自己的命令

`alias foo=\`cd /usr; ls; cd -``设置了自己的别名，可以执行`cd /usr`,`ls`,`cd -`这三条命令 
这样设置别名当shell关闭时会消失，可以把别名添加到文件中去，每次登录系统，这些文件会建立系统环境。