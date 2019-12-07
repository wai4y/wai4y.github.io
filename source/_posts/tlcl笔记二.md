---
title: tlcl笔记二
date: 2017-12-12 22:19:45
categories:
- 2017
- 12月
tags:
- linux
---

## 重定向 I/O redirection

命令的输入来自文件，输出也存到文件

• cat - Concatenate files 连接文件

• sort - Sort lines of text 排序文本行

• uniq - Report or omit repeated lines 显示或省略重复行

• grep - Print lines matching a pattern 打印匹配的参数

• wc - Print newline, word, and byte counts for each file 打印文件中行，字和字节个数

• head - Output the first part of a file 输出文件的第一部分

• tail - Output the last part of a file 数据文件的最后一部分

• tee - Read from standard input and write to standard output and files

### 重定向标准输出

使用`>`重定向符号，其后跟着文件名 

`ls -l /usr/bin > ls-output.txt`可以把命令的运行结果输出到文件中去（不包含错误信息）

ls程序不把它的错误信息输出到标准输出，上面语句只重定向了标准输出，没有重定向标准错误

使用`>`重定向输出结果时，目标文件总是从开头被重写，重定向操作文件被重写文件，导致内容错误

如果要删除一个文件内容，可以使用`> ls_output.txt` 

没有命令在重定向符号`>`之前，会删除一个已经存在文件的内容或是创建一个新的空文件

把重定向结果追加到文件内容后面使用：**>>** 操作符

<!--more-->

### 重定向标准错误

`ls -l /bin/usr 2> ls-error.txt`文件描述符2，紧挨着放在重定向操作符之前，执行重定向标准错误到文件

### 重定向标准输出和错误到同一个文件

`ls -l /bin/usr > ls-output.txt 2>&1`

### cat-连接命令

cat命令读取一个或多个文件，然后复制它们到标准输出

`cat [file]`

大多数情况下，你可以认为cat命令相似于`type`命令

通常用来显示简短的文本，cat可以接受不止一个文件作为参数

`cat movie.mpeg.0* >movie.mpeg`

cat可以用来创建简短的文本

`cat > test.txt xxxxx文本`

`ctrl+d`来告诉cat，在标准输出中，它已经到达了文件末尾（EOF）

#### 重定向标准输入

`cat < test.txt`

使用`<`重定向操作符，我们把标准输入源从键盘改到test.txt

### 管道线

命令可以从标准输入读取数据，然后再把数据输送到标准输出，命令的这种能力被一个shell特性所利用，这个特性叫做管道线。使用管道操作符 `|`，一个命令的标准输出可以以管道到另一个命令的标准输入：

`command1 | command2`

`ls -l /usr/bin | less`

### 过滤器

把几个命令放在一起，组成一个管道线。通常，以这种方式使用的命令被称为过滤器，

`ls /bin /usr/bin | sort |less`

因为我们指定了两个目录(/bin 和/usr/bin),ls 命令的输出结果由有序列表组成,各自针
对一个目录。通过在管道线中包含 sort,我们改变输出数据,从而产生一个有序列表。

### `uniq` - 报道或忽略重复行

uniq命令经常和sort命令结合在一起使用

`ls /bin /usr/bin | sort | uniq | less`

使用uniq从sort命令的输出结果中，来删除任何重复行，如果想看到重复的数据列表，让`uniq`命令带上`-d`选项

`ls /bin  /usr/bin |sort | uniq -d | less`

### `wc` - 打印行，字和字节数

`wc(word count)` 用来显示文件所包含的行，字和字节数

`wc ls-output.txt`

`ls /bin /usr/bin | sort |uniq | wc -l`

`-l`想限制只能显示行数

### `grep`- 打印匹配行

用来找到文件中的匹配文本

`grep pattern [file...]`

在程序列表中，找到文件名中包含单词“zip”的所有文件，

`ls /bin /usr/bin | sort | uniq | grep zip`

参数

* `-i` 忽略大小写
* `-v`只打印不匹配的行

### `head / tail ` - 打印文件开头部分 / 结尾部分

默认打印十行 可以自行添加参数控制打印行数

`-n 5` 打印5行

`head -n 5 ls-output.txt`

也能用用在管道线中

`ls /usr/bin | tail -n 5`

`tail -f /var/log/message` 

使用`-f`选项，`tail`命令会继续监测这个文件，当新的内容添加到文件后，它们会立即出现在屏幕上

### `tee` - 从Stdin 读取数据，并同时输出到Stdout和文件

为了和我们的管道隐喻保持一致,Linux 提供了一个叫做 tee 的命令,这个命令制造了一
个 “tee”,安装到我们的管道上。tee 程序从标准输入读入数据,并且同时复制数据到标准输出
(允许数据继续随着管道线流动)和一个或多个文件。当在某个中间处理阶段来捕捉一个管道
线的内容时,这很有帮助。这里,我们重复执行一个先前的例子,这次包含 tee 命令,在 grep
过滤管道线的内容之前,来捕捉整个目录列表到文件 ls.txt:

`ls /usr/bin | tee ls.txt | grep zip`

---



##从shell眼中看世界

* echo -display a line of a text

### (字符)展开

`echo this is a text`

### 路径名展开

`echo D* > Desktop Documents`

`echo [[:upper:]]*`

默认不会显示隐藏文件，如果输入`echo .*`就会显示隐藏文件

### 波浪线展开

`echo ~`当它用在一个目录的开头时，会展开指定用户的家目录，如果没有指定用户名，则是当前用户的家目录

### 算术表达式展开

`echo $((2+2))`使用算术表达式展开，可以把shell当作计算器来使用

算术表达式的格式：`$((expression))`

### 参数展开

`echo $USER`查看“USER”变量的参数，显示用户名

**查看有效的变量列表**

`printenv | less`

### 命令替换

命令替换允许我们把一个命令的输出作为一个展开模式来使用

```
 echo $(ls)
 输入如下：
 arc-flatabulous-theme arc-icon-theme Calibre Library Desktop Dev dircolors-solarized Documents Downloads examples.desktop fcitx-qt5 IdeaProjects java_error_in_IDEA_13844.log Music Nutstore Pictures Public PycharmProjects Qt5.7.0 Release.key sublime sublime-text-imfix Templates Videos vim-colors-solarized VirtualBox VMs Wall WebstormProjects WizNote

```

`ls -l $(which cp)`

### 双引号

可以使用双引号阻止单词分割，得到期望的结果

`ls -l "two words.txt"`

