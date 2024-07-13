---
sidebar: heading
title: Redis底层实现
category: 缓存
tag:
  - Redis
head:
  - - meta
    - name: keywords
      content: Redis底层实现
  - - meta
    - name: description
      content: Redis常见知识点和面试题总结，让天下没有难背的八股文！
---

# 

## string

字符串对象的编码可以是 int 、 raw 或者 embstr 。

1. 如果一个字符串对象保存的是整数值， 并且这个整数值可以用 long 类型来表示， 那么会将编码设置为 int 。
2. 如果字符串对象保存的是一个字符串值， 并且这个字符串值的长度大于 39 字节， 那么字符串对象将使用一个简单动态字符串（SDS）来保存这个字符串值， 并将对象的编码设置为 raw 。
3. 如果字符串对象保存的是一个字符串值， 并且这个字符串值的长度小于等于 39 字节， 那么字符串对象将使用 embstr 编码的方式来保存这个字符串值。

| 值                                                           | 编码                |
| :----------------------------------------------------------- | :------------------ |
| 可以用 `long` 类型保存的整数。                               | `int`               |
| 可以用 `long double` 类型保存的浮点数。                      | `embstr` 或者 `raw` |
| 字符串值， 或者因为长度太大而没办法用 `long` 类型表示的整数， 又或者因为长度太大而没办法用 `long double` 类型表示的浮点数。 | `embstr` 或者 `raw` |

## hash

hash类型内部编码有两种：

1. ziplist，压缩列表。当哈希类型元素个数小于512个，并且所有值都小于64字节时，Redis会使用ziplist作为哈希的内部实现。ziplist使用更加紧凑的结构实现多个元素的连续存储，更加节省内存。
2. hashtable。当哈希类型无法满足ziplist的条件时，Redis会使用hashtable作为哈希的内部实现，因为此时ziplist的读写效率会下降，而hashtable的读写时间复杂度为O（1）。

使用 ziplist 作为 hash 的底层实现时，添加元素的时候，同一键值对的两个节点总是紧挨在一起， 保存键的节点在前， 保存值的节点在后。

使用场景：记录博客点赞数量。`hset MAP_BLOG_LIKE_COUNT blogId likeCount`，key为MAP_BLOG_LIKE_COUNT，field为博客id，value为点赞数量。

## list

列表list类型内部编码有两种：

1. ziplist，压缩列表。当列表中的元素个数小于512个，同时列表中每个元素的值都小于64字节时，Redis会选用ziplist来作为列表的内部实现来减少内存的使用。
2. 当列表类型无法满足ziplist的条件时，Redis会使用linkedlist作为列表的内部实现。

Redis3.2版本提供了quicklist内部编码，简单地说它是以一个ziplist为节点的linkedlist，它结合了ziplist和linkedlist两者的优势，为列表类型提供了一种更为优秀的内部编码实现。

![](http://img.topjavaer.cn/img/list-api.png)

使用场景：

1. 消息队列。Redis的lpush+brpop命令组合即可实现阻塞队列。

## set

集合对象的编码可以是 intset 或者 hashtable 。

1. intset 编码的集合对象使用整数集合作为底层实现， 集合对象包含的所有元素都被保存在整数集合（数组）里面。
2. hashtable 编码的集合对象使用字典作为底层实现， 字典的每个键都是一个字符串对象， 而字典的值则全部被设置为 NULL 。

## zset

有序集合的编码可以是 ziplist 或者 skiplist 。当有序集合的元素个数小于128，同时每个元素的值都小于64字节时，Redis会用ziplist来作为有序集合的内部实现，ziplist可以有效减少内存的使用。否则，使用skiplist作为有序集合的内部实现。

1. ziplist 编码的有序集合对象使用压缩列表作为底层实现， 每个集合元素使用两个紧挨在一起的压缩列表节点来保存， 第一个节点保存元素的成员（member）， 而第二个元素则保存元素的分值（score）。压缩列表内的集合元素按分值从小到大进行排序。
2. skiplist 编码的有序集合对象使用字典和跳跃表实现。使用字典查找给定成员的分值，时间复杂度为O(1) （跳跃表查找时间复杂度为O(logN)）。使用跳跃表可以对有序集合进行范围型操作。

## 使用场景

string：1、常规key-value缓存应用。常规计数如微博数、粉丝数。2、分布式锁。

hash：存放结构化数据，如用户信息（昵称、年龄、性别、积分等）。

list：热门博客列表、消息队列系统。使用list可以构建队列系统，比如：将Redis用作日志收集器，多个端点将日志信息写入Redis，然后一个worker统一将所有日志写到磁盘。

set：1、好友关系，微博粉丝的共同关注、共同喜好、共同好友等；2、利用唯一性，统计访问网站的所有独立ip 。

zset：1、排行榜；2、优先级队列。

