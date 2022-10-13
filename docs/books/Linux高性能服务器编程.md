---
layout: default
title: Linux高性能服务器编程
parent: books
last_modified_date:  2022年 10月 13日 星期四 16:21:47 CST

---

# 第一篇

## TCP/IP 协议族

> 主要将了数据在协议族中是如何传递的（即数据封装和分用）
> 
> 需要了解每个网络层对应的几个重要协议(如ICMP，TCP，UDP，IP，ARP/RARP)

### 数据的封装

> 各层数据------>>  （数据链路层）帧

上层协议是通过封装(对该层的数据报添加头部信息）来使用下层协议提供的服务的。

### 数据的分用

> 帧 -------->> 各层的数据

对于每一层的 数据报 都可以看作 --  [头部] +[数据]

各层协议通过数据报中的头部信息来决定递交给上层哪个协议。

所以每层协议的数据报的[头部信息]中都会有标识[数据部分]是哪个协议的数据。

## IP协议详解

利用 `sudo tcpdump -ntv -i <网卡id> icmp` 进行抓包

`ping www.baidu.com -s 1475`

> 帧的MTU为1500，ICMP数据报文（包含头部信息(8字节)和数据）,IP头部为20字节
> 
> 故`ping www.baidu.com -s 1475` 的一个ip数据包大小为
> 
> [ip头部(20字节)]+[ICMP数据报文([ICMP头部信息(8字节)]+[数据(1475字节)])]=1503
> 
> 故该ip数据报会被分片:
> 
>     [ip头部(20字节)]+[[ICMP头部信息(8字节)]+[数据(1472字节)]]=1500字节
> 
> 和 [ip头部(20字节)]+[数据(3字节)] = 23字节

得到

```cpp
IP (tos 0x0, ttl 64, id 38832, offset 0, flags [+], proto ICMP (1), length 1500)
    172.27.225.161 > 182.61.200.7: ICMP echo request, id 1, seq 1, length 1480
IP (tos 0x0, ttl 64, id 38832, offset 1480, flags [none], proto ICMP (1), length 23)
    172.27.225.161 > 182.61.200.7: ip-proto-1
```

## TCP协议详解
