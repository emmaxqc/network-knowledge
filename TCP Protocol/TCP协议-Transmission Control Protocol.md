[TOC]


----------


## 1. 定义
 - TCP（Transmission Control Protocol）协议，即**传输控制协议**，属于<font color=red>传输层</font>通信协议
 - 基于TCP的应用层协议有<font color=red>HTTP、HTTPS、FTP、Telnet、POP3、SMTP</font>，要求通信数据准确无误的传送给接收方


----------


## 2. 特点
|特点|具体描述|
|-|-|
|面向连接|使用TCP协议进行数据传输之前，必须先建立TCP连接；<br>数据传输完毕后，需要断开TCP连接|
|面向字节流|数据以流的形式进行传输|
|全双工通信|TCP连接建立后，通信双方均可以向对方发送数据|
|可靠|通过TCP协议传输的数据不丢失、无差错、不重复、按序到达|
<font size=2>**数据以流的形式传输：**
- TCP一次传输的报文长度有限，若传输数据太大，则需要分块多次传输
- 但是由于TCP协议的可靠性，使得接收方可按顺序接受多个数据块，重新组成分块之前的数据流
- 所以TCP看起来就像双方直接传输数据流一样，即 面向字节流</font>


----------


## 3. 优缺点
- 优点：数据传输可靠
- 缺点：效率低，需要建立连接、通信双方发送确认包等机制


----------


## 4. 报文格式
- TCP报文 = 首部（最少20字节） + 数据部分
- 首部前20个字节为固定字符，后4n个字节根据实际需要而增加的可选部分
![TCP报文首部示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E9%A6%96%E9%83%A8%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

|字段|作用|备注|
|--|-|-|
|源端口|报文发送方端口号|16bit|
|目的端口|报文接受方端口号|16bit|
|序号|报文段序号，表示本报文段所发送数据的第1个字节的序号|32bit|
|确认号|期望收到对方下1个报文段的第1个字节的序号；<br>若确认号=N，则表示到序号N-1为止的所有数据都已正确接收|32bit|
|数据偏移|表示TCP首部长度，即数据从何处开始|4bit|
|保留|保留字段，全0|6bit|
|目的端口|报文接受方端口号|16bit|
|URG|urgent，表示紧急指针是否有效|1bit|
|ACK|acknowledgment，1表示这是一个确认TCP包，0表示不是确认包|1bit|
|PSH|push，1表示要求接收端尽快把数据交给应用层，0表示可以先缓存起来|1bit|
|RST|reset，1表示释放当前已经混乱的传输连接，然后可以重新建立新的传输连接|1bit|
|SYN|synchronization，同步，1表示这是一个连接请求或连接确认的报文，仅在三次握手时有效|1bit|
|FIN|finish，1表示数据已全部发送或接受完毕，0表示正常传输|1bit|
|窗口|表示发送该报文的一方还可以接收的最大数据段的窗口大小|32bit|
|校验和|对TCP报文的头部+数据部分+为头部进行校验，双端计算结果一致则表示数据正确|16bit|
|紧急指针|本数据段中紧急数据中的最后一个字节的序号|16bit|
|选项|最长可达40字节，常见的可选项包括MSS最大数据段大小、Timestamp时间戳、<br>SACK选择性确认等|可变|


----------


## 5. 建立连接的过程
### 5.1 三次握手
![TCP三次握手示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

![TCP三次握手示意图详解](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E7%A4%BA%E6%84%8F%E5%9B%BE%E8%AF%A6%E8%A7%A3.png)

> - TCP提供全双工通信，通信双方均可随时向对方发送数据
> - 三次握手期间，任何1次未收到对面的回复，都会重发数据

### 5.2 为什么需要三次握手
**结论：**为了防止服务器端收到*早已失效的客户端连接请求报文*，而一直处于等待客户端发送确认报文的状态，避免*形成死锁，造成资源浪费*
![TCP为什么需要三次握手](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B.jpg)

> [SYN洪泛攻击](https://yuelng.github.io/2017/11/30/computer_science/study_flooding/)：

 >- 服务端的TCP资源分配时刻为完成第二次握手时；
 >- 客户端的TCP资源分配时刻为完成第三次握手时；
 >- 这就使得服务器易于受到SYN洪泛攻击，即同时多个客户端发起连接请求，从而需进行多个请求的TCP连接资源分配，维护多个半成型的TCP连接，而忽略的用户的正常请求


----------

 
## 6. 连接释放的过程
### 6.1 四次挥手
![TCP四次挥手示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

![TCP四次挥手示意图详解](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B%E7%A4%BA%E6%84%8F%E5%9B%BE%E8%AF%A6%E8%A7%A3.png)

### 6.2 为什么需要四次挥手
**结论：**为了保证通信双方都能知会到对方需要释放连接，且最终能成功断开连接
![TCP为什么需要四次挥手](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B.jpg)

### 6.3 客户端为什么需要进入TIME-WAITE状态
**原因1：**保证客户端最后发送的连接释放确认报文成功到达服务器，使得服务器能够正常关闭连接
![TCP四次挥手中TIME-WAITE状态](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B%E4%B8%ADTIME-WAITE%E7%8A%B6%E6%80%81%E8%A7%A3%E9%87%8A.jpg)

**原因2：**防止已失效的连接请求报文出现在本次TCP连接通信过程中；客户端发送了最后1个连接释放请求确认报文后，再经过2MSL时间，则可使本连接持续时间内所产生的所有报文段都从网络中消失。

> MSL = 最长报文段寿命（Maximum Segment Lifetime），超过时间，网络中的报文将被丢弃


----------

## 7. TCP连接中的各种保护措施
### 7.1 滑动窗口协议

|类型|定义|作用|
|-|-|-|
|发送窗口|任意时刻，发送方维持的一组连续的、允许发送的帧序号|对发送方进行流量控制；发送窗口大小：还未收到对方确认信息时，发送方最多还可发送的数据帧数量|
|接收窗口|任意时刻，接收方维持的一组连续的、允许接收的帧序号|指明接收方可接收的数据帧序号；只有当帧序号落在接收窗口内才允许将该帧手下，否则一律丢弃|

**发送方：**

 - 每收到一个接收方发送过来的确认帧后，发送窗口向后滑动一个帧的距离
 - 当发送窗口内所有帧全部发送但未收到确认帧时，停止发送，直到收到对方的确认帧

![TCP滑动窗口协议发送方](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3%E5%8D%8F%E8%AE%AE%E5%8F%91%E9%80%81%E6%96%B9.png)

**接收方：**

 - 每收到一个发送方发送过来的数据帧后，接收窗口向后滑动一个帧的距离，并发回确认帧
 - 确认帧包含2个重要信息：
    -  期望接收到的下1个字节的序号N，同时表示已经正确收到钱N-1个字节数据
    -  当前接收窗口大小M，发送方根据N和M计算出还可以发送多少数据给接收方
    -  举例：发送方已经发送到X序号的数据，则还可以发送的字节数为M-(Z-N)
 - 若收到的数据帧在接收窗口外，则一律丢弃

![TCP滑动窗口协议接收方](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3%E5%8D%8F%E8%AE%AE%E6%8E%A5%E6%94%B6%E6%96%B9.png)

**滑动窗口协议——停止等待协议**

 - 发送窗口 = 1，接收窗口 = 1
  - 发送方必须受到接收方的确认帧后，才能发送下一帧
  - 接收方每收到一帧数据，必须发送一个确认帧
  - 发送方必须等待接收方的确认帧，才可以发送下一帧
 - 特点：效率低，宽带利用率低
 - 应用场景：网络环境差、或宽带本身很低的情况

**滑动窗口协议——回退N帧协议**

 - 发送窗口M > 1，接收窗口 = 1
  - 发送方可以连续发送M帧数据，而不需要等待对方确认
  - 接收方采用**累计确认+后退N帧**策略，只允许按顺序接收数据帧
 - 特点：网络状态良好时提升效率，但网络状态差时，导致大量数据重发，进一步恶化网络状况
![TCP滑动窗口回退N帧](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3%E5%8D%8F%E8%AE%AE%E5%90%8E%E9%80%80N%E5%B8%A7.png)

**滑动窗口协议——选择重传协议**

 - 发送窗口 > 1，接收窗口 > 1
 - 选择重传协议是为了解决回退N帧协议中可能会进一步恶化网络的问题
 - 原理：接收端总会缓存所有收到的帧，当某个帧出现错误时，只会要求重传这一个帧；当某个序号后的所有帧都接收正确后，才会一起提交给高层
 - 缺点：接收端需要提供缓存空间

### 7.2 流量控制

 - 目的：避免因为发送方发送数据过快，导致接收方缓存溢出（发送接收速度不匹配）
 - 措施：通过接收方的ACK帧中的Window字段，动态调整调整发送方的发送窗口大小（主要方法有停止等待、回退N帧、选择重传）
 - 结果：降低发送速率，来配合接收方的读取速率（由接收方来控制）

![TCP流量控制](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

*注意：通过可变窗口实现流量控制时要注意死锁问题，通过设置持续计时器来打破死锁*
![TCP流量控制死锁问题](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6%E6%AD%BB%E9%94%81%E9%97%AE%E9%A2%98%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

### 7.3 拥塞控制

 - 面向整个通信网络，防止过多的数据进入网络，使得网络中的路由器、数据链路过载
 - 措施：
  - 慢开始 & 拥塞避免
  - 快重传 & 快恢复

### 7.3.1 拥塞窗口
  - 定义：发送方根据自己估计的网络拥塞状态，维持一个状态变量，即拥塞窗口（cwnd）
  - 作用：拥塞窗口反映当前网络容量、拥塞状态
  - 大小：动态变化，取决于网络的拥塞程度
  - 目的：让发送方的发送窗口 = 拥塞窗口
  - 原则：当网络无拥塞时，增大拥塞窗口，以便发送方发送更多的分组；当网络拥塞时，建校拥塞窗口，以减少注入到网络中的分组数

### 7.3.2 慢开始 & 拥塞控制
慢开始：发送方开始发送数据时，由小到大逐渐增大拥塞窗口数值（即增大发送窗口）

  - 开始发送报文时，将cwnd设置为1个最大报文段MSS的数值，即cwnd=1（试探网络状态）
  - 每收到一个传输轮次的确认报文后，将cwnd设置为上一轮次的2倍
  - 传输轮次：把拥塞窗口中所有允许发送的报文全部发送，且收到对方发送的最后一个字节的确认报文，这为一个轮次

![TCP慢开始](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6-%E6%85%A2%E5%BC%80%E5%A7%8B.png)

拥塞控制：使拥塞窗口cwnd以现行规律缓慢增长，每经过一个报文往返时间RTT，发送方的cwnd增加1

![TCP拥塞控制](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6-%E6%8B%A5%E5%A1%9E%E9%81%BF%E5%85%8D.png)

*举例说明：*
*cwnd：拥塞窗口值*
*ssthresh：慢开始阶段的门限值；*
*目的：当发送网络拥塞时，迅速减少发送到网络中的数据报文，同时使得发生拥塞的路由器有足够的时间处理队列中积压的分组*
![TCP拥塞控制示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6%E8%BF%87%E7%A8%8B%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

### 7.3.2 快重传 & 快恢复
快重传&快恢复解决方案是对慢开始&拥塞控制方案的改进

**快重传：**

  - 接收方每收到一个错误或失序的报文后，立刻发送重复确认报文，使得发送方尽快知道有报文需要重复传送
  - 发送方一连收到3个重复确认报文后，立刻重发对方尚未收到的报文，而不必等待设置的报文重传计时器到期
  - 发送方能尽早发送缺失的报文，使得整个网络吞吐量提高

![TCP快重传](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6-%E5%BF%AB%E9%87%8D%E4%BC%A0.png)

**快恢复：**当发送方连续收到3个重复确认报文时，进入快恢复阶段

 - 执行乘法减小算法：把慢开始门限ssthresh设置为出现拥塞时发送放发送窗口大小的一半，得到新的慢开始门限值
 - 将拥塞窗口cwnd设置为新的慢开始门限值（跳过了慢开始中cwnd设置为1的阶段，因为此时收到3个重复确认报文，表示当前网络不会拥塞）
 - 执行加法增大算法：执行拥塞控制算法，使cwnd线性增长

*举例说明：*
![TCP拥塞控制示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/TCP%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6%E8%BF%87%E7%A8%8B%E7%A4%BA%E6%84%8F%E5%9B%BE-%E5%BF%AB%E9%87%8D%E4%BC%A0-%E5%BF%AB%E6%81%A2%E5%A4%8D.png)
