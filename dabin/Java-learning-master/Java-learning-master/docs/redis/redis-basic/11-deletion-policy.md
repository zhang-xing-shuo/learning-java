---
sidebar: heading
title: Redis删除策略
category: 缓存
tag:
  - Redis
head:
  - - meta
    - name: keywords
      content: Redis删除策略
  - - meta
    - name: description
      content: Redis常见知识点和面试题总结，让天下没有难背的八股文！
---

# 删除策略

1. 被动删除。在访问key时，如果发现key已经过期，那么会将key删除。
2. 主动删除。定时清理key，每次清理会依次遍历所有DB，从db随机取出20个key，如果过期就删除，如果其中有5个key过期，那么就继续对这个db进行清理，否则开始清理下一个db。

3. 内存不够时清理。Redis有最大内存的限制，通过maxmemory参数可以设置最大内存，当使用的内存超过了设置的最大内存，就要进行内存释放， 在进行内存释放的时候，会按照配置的淘汰策略清理内存，淘汰策略一般有6种，Redis4.0版本后又增加了2种，主要由分为三类：

   - 第一类 不处理 noeviction。发现内存不够时，不删除key，执行写入命令时直接返回错误信息。（默认的配置）

   - 第二类 从所有结果集中的key中挑选，进行淘汰

     - allkeys-random 就是从所有的key中随机挑选key，进行淘汰
     - allkeys-lru 就是从所有的key中挑选最近最少使用的数据淘汰
     - allkeys-lfu 就是从所有的key中挑选使用频率最低的key，进行淘汰。（这是Redis 4.0版本后新增的策略）

   - 第三类 从设置了过期时间的key中挑选，进行淘汰

     这种就是从设置了expires过期时间的结果集中选出一部分key淘汰，挑选的算法有：

     - volatile-random 从设置了过期时间的结果集中随机挑选key删除。
     - volatile-lru 从设置了过期时间的结果集中挑选最近最少使用的数据淘汰
     - volatile-ttl 从设置了过期时间的结果集中挑选可存活时间最短的key开始删除(也就是从哪些快要过期的key中先删除)
     - volatile-lfu 从过期时间的结果集中选择使用频率最低的key开始删除（这是Redis 4.0版本后新增的策略）



