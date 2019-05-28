[TOC]

# HTTPS协议详解

## 1. 定义（与HTTP的区别）

 - HTTP，超文本传输协议，为客户端浏览器或其他程序与web服务器之间的***应用层***通信协议
 - HTTPS（HyperText Transform Protocol over Secure Socket Layer），可以理解为HTTP+SSL/TLS，即HTTP加上SSL层或者TLS层，HTTPS的安全基于SSL

![HTTP与HTTPS的区别1][1]

![HTTP与HTTPS的区别2][2]

*SSL（Secure Socket Layer，安全套接字层）：位于TCP/IP协议与各种应用层协议之间，为数据通信提供安全支持*

*TLS（Transport Layer Security，传输层安全）：前身为SSL，由于SSL3.0和TLS1.0存在安全漏洞，目前正在对TLS进行持续改进*
  
## 2. HTTP访问过程和缺陷
### 2.1 HTTP访问过程
![HTTP的访问过程][3]

*抓包过程如下：首先是TCP三次握手，然后以明文形式传输数据*
![HTTP的抓包过程][4]

*容易发生黑客劫持：黑客冒充服务器，向客户端发送错误数据*
![HTTP的抓包过程][5]

### 2.2 HTTP访问缺陷
HTTP传输面临的风险有：

 - 窃听风险：黑客可以获知通信内容。
 - 篡改风险：黑客可以修改通信内容。
 - 冒充风险：黑客可以冒充他人身份参与通信。

## 3. HTTP向HTTPS演化的过程

### 3.1 加密传输

 - 对称加密：双方拥有相同的秘钥，保证数据传输安全性，但通信双方需要维护大量秘钥，且客户端/服务器安全级别不同，秘钥维护成本高
![对称加密][6]
 
 - 非对称加密：客户端采用公钥加密，服务器使用私钥对内容解密，反之亦然；但由于黑客也拥有相同的公钥，在第4步时黑客可劫持服务器响应报文，使用公钥对其进行解密获取其中的内容
![非对称加密][7]

 - 对称加密+非对称加密：客户端在第3步中使用公钥对内容进行加密，内容为后续传输使用的对称算法和对称秘钥存在的问题是客户端如何获取真实的公钥，以及无法验证服务器的真实性
![对称加密+非对称加密][8]

### 3.2 SSL/TLS证书传输

 ![SSL证书传输][9]

（1）Client Hello，客户端明文请求服务器，请求内容包括：

- SSL/TLS版本号（目前基本为TLS1.1，TLS1.2）
- 客户端支持的加密套件（包含协议类型，非对称|对称加密算法，校验时的hash算法）
- 客户端生成的随机数random_C
- 数据压缩算法（客户端支持的压缩算法）
- extension拓展字段，各种辅助信息，其中通过server\_name字段来判断服务器是否支持SNI（Server Name Indicate）

> 所谓SNI是为了解决多域名共享IP的情况。由于HTTPS中服务器证书是和域名绑定的，如果不支持SNI则有可能导致服务器下发的证书校验失败，从而导致SSL握手失败。SNI支持在server\_name字段中指定服务器域名，这样可以保证不会因为下发错误证书导致握手失败了

（2）服务器准备下发证书

（3）Server Hello，服务器包含3个动作

- 服务器返回协商结果，包括选择使用的协议版本，选择的加密套件，选择的压缩算法，随机数random_S等
- 服务器向客户端下发证书，包括证书发布机构CA，证书有效期，公钥，证书所有者，签名等，其中签名采用服务器私钥加密
- 服务器通知客户端Server Hello结束

> 举例：TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> TLS：使用的协议名称
> ECDHE_RSA：非对称加密算法，用于身份验证
> AES_128_GCM：对称加密算法，后续通信时使用的对称加密算法
> SHA256：数字摘要的hash算法

（4）客户端校验

- 验证发证机构CA（浏览器遍历OS内置的受信任证书发布机构CA，与服务器发来的证书进行对比）、证书有效期、域名等信息
- 使用证书提供的hash算法，对证书明文进行hash计算，得到计算后的值`hash_cal`；从OS中提取颁发者CA的公钥，对发来的证书中的签名进行解密，得到`hash_pem`；若`hash_cal=hash_pem`，则表示证书合法
- 浏览器使用相同的hash算法，对服务器发来的证书进行hash计算，将计算后的hash与签名进行对比，若相同，则可信
- 最终浏览器读取证书中的公钥，用于后续加密

（5）客户端生成后续对称加密通信的对称秘钥

 - 客户端生成一个随机数Pre-Master，得到最终的对称秘钥`enc_key=Fuc(random_C, random_S, Pre-Master)`，采用证书内的公钥对enc_key进行加密，并和对称加密算法一起，发送给服务器
    
（6）服务器采用自己的私钥进行解密，获得后续通行使用的对称加密秘钥和加密算法

（7）（8）使用协商好的加密算法和公私钥开始通信

## 4. 证书链

 - 在CA根证书和服务器证书中间增加一级或多级证书机构，即中间证书，所有证书产生的原理和验证方法不变。

![证书链][10]

 - 证书链的验证是自下而上的信任传递过程，以百度的证书举例：
    - baidu.com的证书被Validation CA验证
    - Validation CA的合法性被Root CA验证
    - 最终证明baidu.com这个服务器证书的合法性

![百度证书][11]

 - 证书链的优势：
    - 减少根证书的管理工作量，提高证书审核和签发效率
    - 根证书一般内置在客户端内，但私钥放在RootCA机构进行物理隔离存储，一般丢失可能性较小
    - 中间证书私钥放在在线服务器上，私钥丢失后，可通过根证书重新生成新的私钥，签发新的中间证书；已经泄露的私钥所签发的证书全部吊销
 - 证书链的劣势：

> 证书发行商们为了安全，通常会将这些根证书对应的私钥保存在绝对断网的金库里。在金库里用这些根私钥，签发一些“中级”证书，这些中级证书的私钥拥有签发下一级证书的权限。这些中级私钥被安装到在线服务器上，通过签发网站证书来赚钱。

> 一旦这些服务器被黑，发行商就可以利用金库里物理隔离的根证书私钥，可以签发吊销令，消灭这些中级证书的信任，而不必让各家浏览器彻底不信任这家发行商的根证书。再签一条新的中级发行证书，又是一条能赚钱的好汉。

## 5. HTTPS证书吊销

*CA机构能够签发证书，同样也可以吊销或废弃证书，主要通过CRL和OCSP(Online Certificate Status Protocol，证书状态在线查询协议)来实现。*

 - CRL(Certificate Revocation List，证书吊销列表)

CRL是一个单独的文件，包含CA当前已经吊销的证书序列号和吊销日期，服务器发送的证书中通常会包含一个URL地址，告诉客户端去哪里下载对应的CRL以校验证书是否被吊销。

CRL不需要频繁更新已吊销证书列表，维护成本可控，但不能及时吊销证书，容易造成损失。

 - OCSP(Online Certificate Status Protocol，证书状态在线查询协议)

OCSP可实时在线查询证书是否被吊销，服务器下发的证书中一般包含OCSP的URL地址，提供给客户端进行在线实时查询。

*在实际应用中，CRL和OCSP机制是互补的，大多数证书机构同时提供两种机制。*


## 6. SSL/TLS会话恢复

*完整的TLS握手需要额外的延迟和计算，为所有需要安全通行的应用来带严重的性能损耗。为了减少损耗，TLS提供恢复机制，或多个连接之间共享相同的协商对称加密秘钥进行数据通信。*

 - **会话标识**
服务器在“Server Hello”阶段，构建和发送一个32字节的会话标识符作为“Server Hello”消息的一部分。

### 6.1 HTTPS会话复用——Session ID
*缩减的TLS握手流程：*
![https会话缓存][12]

步骤如下：

 - 如果客户端和服务器之前进行过https通信，在结束通信后，服务器会返回客户端的Session ID，客户端和服务器均维护一个Session ID和其对应的协商参数的缓存。
 
 - 客户端再次发起https请求时，在client hello阶段带上Session ID发送给服务器，服务器检索Session缓存
    - 若没找到或过期，则按正常TLS握手流程进行
    - 若找到，则用缓存的协商秘钥加密当前通讯参数返回客户端。
 
 - 客户端验证后也用协商秘钥加密当前通讯参数返回服务器，服务器验证通过后，直接使用该秘钥和客户端进行后续通信。

*大多数现代浏览器都会有意的等待第一轮TLS连接完成后，再打开到同一台服务器的新连接：后续TLS连接，可以重复使用之前协商好的参数，以避免握手的延迟和损耗。*

**“会话标识”策略缺陷：**
 - 服务器需要为每个客户端创建和维护一个session缓存，服务器内存消耗非常大
 - 对一些流量大的网站来讲，session ID如何清楚和过期是一个非常大的问题，在构建初期需要认真思考和规划

### 6.2 HTTPS会话复用——Session Ticket
*为了解决服务器缓存和维护Session ID带来的问题，Session Ticket机制被引入，目的是消除服务器需要维护大量Session ID的要求。*

步骤：

 - 若客户端支持“Session Ticket”，在TLS握手最后一步，服务器向客户端发送一个包含“New Session Ticket”的信息（包含一个加密通信所需要的全部协商信息），这些数据采用只有服务器持有的秘钥进行加密。
 - Session Ticket由客户端存储，并在下一次https通信时，添加到client hello消息的SessionTicket扩展字段中。
 - Session Ticket机制要求客户端在每一个新的会话开始前，提供Session Ticket，直到该Ticket过期。

### 6.3 Session ID和Session Ticket的区别

**Session ID：**服务器为每次会话生成并缓存一个ID并发送给客户端，在重连时，客户端向服务器发送该ID，服务器查找缓存后，若匹配则重用之前协商的加密参数。

**Session Ticket：**服务器为每次会话生成一个用服务器私钥加密过的Ticket并发送给客户端，该Ticket仅由客户端保存管理。在重连时，客户端向服务器发送该Ticket，服务器用私钥解密后，重用之前协商的加密参数。

**Session Ticket优势：**由于Session ID往往是存储在一台服务器上，当服务器使用负载均衡技术后，客户端向不同的服务器发送请求时，就无法复用之前的加密参数信息。而Session Ticket可以较好的解决此类问题，因为服务器只需要确认由客户端发送过来的加密参数即可（需要保证所有多点服务器采用相同的秘钥）。


  [1]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTP%E4%B8%8EHTTPS.png
  [2]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20Protocol/HTTP%E4%B8%8EHTTPS%E7%9A%84%E5%8C%BA%E5%88%AB.png
  [3]:https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTP%E8%AE%BF%E9%97%AE%E8%BF%87%E7%A8%8B%E4%BB%A5%E5%8F%8A%E9%A3%8E%E9%99%A91.png
  [4]:https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTP%E8%AE%BF%E9%97%AE%E8%BF%87%E7%A8%8B%E4%BB%A5%E5%8F%8A%E9%A3%8E%E9%99%A92.jpg
  [5]:https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTP%E8%AE%BF%E9%97%AE%E8%BF%87%E7%A8%8B%E4%BB%A5%E5%8F%8A%E9%A3%8E%E9%99%A93.png
  [6]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTPS%E6%BC%94%E5%8C%96%E8%BF%87%E7%A8%8B-%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86.jpg
  [7]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTPS%E6%BC%94%E5%8C%96%E8%BF%87%E7%A8%8B-%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86.jpg
  [8]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTPS%E6%BC%94%E5%8C%96%E8%BF%87%E7%A8%8B-%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86+%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86.jpg
  [9]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/HTTPS-SSL%E8%AF%81%E4%B9%A6%E5%8A%A0%E5%AF%861.jpg
  [10]:https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/%E8%AF%81%E4%B9%A6%E9%93%BE.png
  [11]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/%E7%99%BE%E5%BA%A6%E8%AF%81%E4%B9%A6%E9%93%BE.png
  [12]: https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTPS%20Protocol/https%E4%BC%9A%E8%AF%9D%E7%BC%93%E5%AD%98.png