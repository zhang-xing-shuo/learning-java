---
sidebar: heading
title: Redis简介
category: 缓存
tag:
  - Redis
head:
  - - meta
    - name: keywords
      content: Redis简介,redis优缺点,io多路复用,Memcached和Redis的区别,redis应用场景
  - - meta
    - name: description
      content: Redis常见知识点和面试题总结，让天下没有难背的八股文！
---

# 简介

Redis是一个高性能的key-value数据库。Redis对数据的操作都是原子性的。

## 优缺点

优点：

1. 基于内存操作，内存读写速度快。
2. Redis是单线程的，避免线程切换开销及多线程的竞争问题。单线程是指在处理网络请求（一个或多个redis客户端连接）的时候只有一个线程来处理，redis运行时不止有一个线程，数据持久化或者向slave同步aof时会另起线程。
3. 支持多种数据类型，包括String、Hash、List、Set、ZSet等
4. 支持持久化。Redis支持RDB和AOF两种持久化机制，持久化功能有效地避免数据丢失问题。
5. redis 采用IO多路复用技术。多路指的是多个socket连接，复用指的是复用一个线程。redis使用单线程来轮询描述符，将数据库的开、关、读、写都转换成了事件。多路复用主要有三种技术：select，poll，epoll。epoll是最新的也是目前最好的多路复用技术。

缺点：对join或其他结构化查询的支持就比较差。

## io多路复用

将用户socket对应的文件描述符（file description）注册进epoll，然后epoll帮你监听哪些socket上有消息到达。当某个socket可读或者可写的时候，它可以给你一个通知。只有当系统通知哪个描述符可读了，才去执行read操作，可以保证每次read都能读到有效数据。这样，多个描述符的I/O操作都能在一个线程内并发交替地顺序完成，这就叫I/O多路复用，这里的复用指的是复用同一个线程。

## 应用场景

1. 缓存热点数据，缓解数据库的压力。
2. 利用Redis中原子性的自增操作，可以用使用实现计算器的功能，比如统计用户点赞数、用户访问数等，这类操作如果用MySQL，频繁的读写会带来相当大的压力。
3. 简单消息队列，不要求高可靠的情况下，可以使用Redis自身的发布/订阅模式或者List来实现一个队列，实现异步操作。
4. 好友关系，利用集合的一些命令，比如求交集、并集、差集等。可以方便搞定一些共同好友、共同爱好之类的功能。
5. 限速器，比较典型的使用场景是限制某个用户访问某个API的频率，常用的有抢购时，防止用户疯狂点击带来不必要的压力。

## Memcached和Redis的区别

1. Redis只使用单核，而Memcached可以使用多核。
2. MemCached数据结构单一，仅用来缓存数据，而Redis支持更加丰富的数据类型，也可以在服务器端直接对数据进行丰富的操作，这样可以减少网络IO次数和数据体积。
3. MemCached不支持数据持久化，断电或重启后数据消失。Redis支持数据持久化和数据恢复，允许单点故障。


