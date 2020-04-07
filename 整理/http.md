# http

三次握手:
    1. 客户端发送 SYN = 1, seq number = x    客户端 SYN_SEND
    2. 服务器收到后, 发送 SYN=1, ACK=1, ack number=x+1, seq number=y   服务端 SYN_RCVD
    3. 客户端收到后, 发送 SYN=0, ACK=1, seq number=x+1, ack number=y+1   ESTABLISHED

四次挥手:
    1. 客户端发送 FIN = 1, seq number = x   客户端 FIN_WAIT_1
    2. 服务端收到后, 发送 ACK=1, ack number=x+1  服务端 CLOSE_WAIT,  客户端收到后 FIN_WAIT_2
    3. 服务端发送 FIN=1, seq number = y     服务端 LAST_ACK

4. 客户端收到后, 发送 ACK=1, ack number=y+1  客户端 TIME_WAIT, 等待2MSL(数据包在网络中的最大生存时间)后 CLOSED, 服务端收到后 CLOSED

为什么三次握手:
    如果两次, 如果第二次消息丢失, 那么客户端认为没有连接, 但是 服务端认为连接了. 这时候服务端发给客户端数据就没法正确接收.

为什么四次挥手:
    因为服务端收到客户端 FIN 报文后, 有可能还在处理数据, 如果等待很长时间, 客户端会重复发送 FIN.
    现在第 2 步表示 服务端已经知道客户端不会再发送数据了, 但是仍然会接收数据
    等服务端处理完之后, 会通过第 3 步告知客户端, 自己不会再发送数据了.

大量 TIME_WAIT 原因:
    如果第 4 步报文丢失, 则 第 3 步会重试, 这时候如果没有客户端 CLOSED, 那么无法处理该报文
    解决: 
        使用长连接
        设置 net.ipv4.tcp_timestamps; 设置 net.ipv4.tcp_tw_reuse, 重用连接; 设置 net.ipv4.tcp_tw_recycle, 内核快速回收连接.

http结构:
    请求:
        1. 请求方法, url, 协议版本
        2. 头部键值对
        3. 请求数据
    响应:
        1. 协议版本, 状态码, 状态描述
        2. 头部键值对
        3. 请求数据

状态码分类:
    100: 信息响应(不太理解)
    200: 成功
    300: 重定向
    400: 客户端错误
    500: 服务端错误

OSI 七层模型:
    物理层
    数据链路层
    网络层
    传输层
    会话层
    表示层
    应用层

TCP/IP 五层模型:
    物理层
    数据链路层
    网络层
    传输层
    应用层

### http code

302: 重定向

401: 未授权

403: 服务器拒绝请求

404: 未找到资源

500: 服务器内部错误

502: 错误网关

### keep-alive

htpp 请求头设置: Connection:keep-alive

根据content-length 或者 Transfer-Encoding：chunk, 长度=0 的 chunk 表示结束

