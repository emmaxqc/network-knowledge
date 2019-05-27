[TOC]

#HTTP协议

## 1 定义
HTTP协议（HyperText Transform Protocol，超文本传输协议）属于最高层的应用层协议，规定了应用之间的通信标准

## 2. 特点

|特点|具体描述|
|-|-|
|传输效率高|a. 无连接：传输数据之前，不需要建立连接<br>b. 无状态：传输过程中，不需要保存任何历史信息和状态信息<br>c. 传输格式简单：请求HTTP传输时，只需要传送请求方法(POST\|GET)和路径|
|传输可靠性高|采用TCP作为运输层传输协议，可靠性建立在TCP的可靠传输上|
|兼容性好|支持B/S，C/S模式|
|灵活性高|HTTP允许传输任意类型的数据对象|

## 3. 工作方式
- HTTP在应用层采取 **请求/响应**的方式进行通信，具体流程如下：

![HTTP工作方式示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E5%B7%A5%E4%BD%9C%E6%96%B9%E5%BC%8F.png)

## 4. HTTP报文格式
- HTTP在应用层采用*报文*的方式来进行数据交互
- HTTP的报文分为**请求报文**&**响应报文**

### 4.1 请求报文格式

*其中URL为请求路径*
![HTTP请求报文示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87%E7%BB%93%E6%9E%84%E5%9B%BE.png)

**请求行说明：**
![HTTP请求行示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87%E2%80%94%E2%80%94%E8%AF%B7%E6%B1%82%E8%A1%8C.png)

*此处说明GET和POST请求方式的区别：*
![HTTP GET和POST区别](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87%E2%80%94%E2%80%94POST%26GET.png)

*举例说明：*

 - 若请求报文采用GET方法、 URL地址为 http://www.tsinghua.edu.cn/chn/yxsz/index.htm；HTTP1.1版本
 - 则请求行是：GET /chn/yxsz/index.htm HTTP/1.1

**请求头说明：**

 - 使用方式：采用“**header（字段名）：value（值）**”的方式
 - 请求和响应报文的通用Header

![HTTP header示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E8%AF%B7%E6%B1%82%26%E5%93%8D%E5%BA%94%E6%8A%A5%E6%96%87%E9%80%9A%E7%94%A8header%E7%BB%93%E6%9E%84%E5%9B%BE.png)

 - 常见请求Header

![HTTP header示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%20%E5%B8%B8%E8%A7%81header%E7%BB%93%E6%9E%84%E5%9B%BE.png)

**请求体说明：**

![HTTP请求体示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E8%AF%B7%E6%B1%82%E4%BD%93%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

**总结和举例：**

*HTTP请求报文总结：*
![HTTP请求报文总结示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87%E7%BB%93%E6%9E%84%E6%80%BB%E7%BB%93.png)

*HTTP请求报文举例说明：*
![HTTP请求报文举例说明](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87%E4%B8%BE%E4%BE%8B%E8%AF%B4%E6%98%8E.png)

### 4.2 响应报文格式

HTTP响应报文包括：**状态行、响应头部、响应正文**三部分，其中响应头部和响应正文与请求报文类似

*响应报文状态行示意图：*
![HTTP响应报文状态行示意图](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E5%93%8D%E5%BA%94%E6%8A%A5%E6%96%87%E7%8A%B6%E6%80%81%E8%A1%8C%E7%BB%93%E6%9E%84%E5%9B%BE.png)

*举例说明：*
*HTTP/1.1 202 Accepted(接受)*
*HTTP/1.1 404 Not Found(找不到)*

***响应报文总结：***
![HTTP响应报文总结](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20%26%20HTTPS%20Protocol/HTTP%E5%93%8D%E5%BA%94%E6%8A%A5%E6%96%87%E6%80%BB%E7%BB%93.png)

## 5. HTTP处理长连接

![HTTP长连接](https://raw.githubusercontent.com/emmaxqc/network-knowledge/master/HTTP%20Protocol/HTTP%E5%A4%84%E7%90%86%E9%95%BF%E8%BF%9E%E6%8E%A5%E8%BF%87%E7%A8%8B%E7%A4%BA%E6%84%8F%E5%9B%BE.jpg)
