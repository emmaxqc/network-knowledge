[TOC]

# 计算机网络通关攻略——UDP协议

----------

## 1. 定义
 - UDP（User Datagram Protocol）协议，即**用户数据报协议**，属于<font color=red>传输层</font>通信协议
 - 基于UDP的应用层协议有<font color=red> TFTP简单文件传输协议，SNMP网络管理，DNS域名转换</font>，


----------

## 2. 特点

|特点|具体描述|
|-|-|
|无连接|传输数据之前，不需要建立连接（类似写信，只需要填好地址交给邮局）|
|面向报文|数据以数据报文的形式传输，UDP报文长度无限制，一次性发送全部数据|
|不可靠|UDP数据报文发送后，不会验证是否正确被收到，即有可能出现丢包|
|无拥塞控制|由于是不可靠传输，即不管是否到达接收方，故不需要拥塞控制|


----------

## 3. 优缺点
- 优点：数据传输速率快
- 缺点：易出现丢包现象


----------


## 4. 报文格式
- UDP报文 = 首部（8字节） + 数据部分
- UDP是面向报文的，即发送方对应用层交付的报文不做合并、拆分操作，而是在添加UDP首部后就向下交付给IP层

![UDP报文示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/UDP%E5%8D%8F%E8%AE%AE%E6%95%B0%E6%8D%AE%E6%8A%A5.jpg)

- UDP数据报文首部示意图：

![UDP报文首部示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/UDP%E5%8D%8F%E8%AE%AE%E6%95%B0%E6%8D%AE%E6%8A%A5%E9%A6%96%E9%83%A8.jpg)

|字段|作用|
|--|-|
|源端口|报文发送方端口号，不需要时置为全0
|目的端口|报文接受方端口号，若接收方发现报文中目的端口号不正确，则丢弃该报文<br>同时由网际控制协议ICMP发送“端口不可达”差错报文给发送方|2字节|
|长度|UDP数据报的长度，最小为8，表示仅有头部|
|校验和|检测UDP数据报在传输过程中是否有错，若有错则丢弃|
|伪头部|包括源IP地址，目的IP地址等，用于计算校验和，不会添加到数据报中|


----------

## 5. UDP和TCP的区别

![UDP报文首部示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/UDP%E5%92%8CTCP%E7%9A%84%E5%8C%BA%E5%88%AB.png)