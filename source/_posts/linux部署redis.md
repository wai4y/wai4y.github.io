---
title: linux部署redis
date: 2017-12-04 20:56:37
categories:
- Linux
tags:
- redis	
---

##  Ubuntu系统部署redis

redis是一个键值对存储仓库，通常被解释成非关系型(NoSQL)数据库，键值对的形式可以方便存储

存储：

`SET server:name "fido"` 设置键值对 key:`server:name`  value: `'fido'`

取值：

`GET server:name => "fido"`

其他操作：

`DEL`用来删除一个给定的key和value

`SET-if-not-exists `叫做`SETNX`用来设置一个不存在的key

`INCR` 可以让key自增

```
SET connections 10 // 设置key
INCR connections  => 11 
INCR connections => 12
DEL connections
INCR connections => 1
```

> 关于`INCR` 的注意事项，在可以手动自增的情况下，为什么要提供一个操作符
>
> ```
> x = GET count
> x = x + 1 
> SET count x
> ```
>
> 这样也可以实现自增，但是这是在同一时刻只有单次请求的情况下才可以，如果多个客户端同时请求，不满足实际需求
>
> `INCR` 是一个具有原子性的操作符 ， 在redis中对不同的数据类型提供了很多这样具有原子性的运算符

--------

<!--more-->

## 安装redis

```
wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable
make
```

在终端依次执行上述命令，编译完成后进入src目录 可以看到如下文件

* **redis-server**  是redis的服务
* **redis-sentinel** 是redis实例的监控管理、通知和实例失效备援服务，是redis集群的管理工具
* **redis-cli** 是redis的命令行交互工具
* **redis-benchmark** 用来检查redis服务的性能
* **redis-check-aof** 和 **redis-check-dump** 用来处理损坏的数据文件

执行如下命令

- sudo cp src/redis-server /usr/local/bin/
- sudo cp src/redis-cli /usr/local/bin/
- sudo cp redis.conf /etc/redis/6379.conf

配置文件做如下配置：

```
sudo mkdir /etc/redis
sudo mkdir /var/redis
sudo mkdir /etc/redis/6379
```



```
daemonize yes # 后台启动
pidfile /var/run/redis_6379.pid
loglevel debug
logfile /var/log/redis_6379.log
dir /var/redis/6379
```



启动 redis: `redis-server`

也可以指定配置文件启动 `redis-server /etc/redis/6379.conf`

关闭redis：强行终止redis进程可能会导致redis持久化数据丢失。

应使用`redis-cli shutdown` 或者在客户端中用`shutdown`



