# TCP 

### 首部包括以下内容：

1. 源端口 source port : 占 2 个 字节
2. 目的端口 destination port : 占 2 个 字节
3. 序号 sequence number: 占 4 个字节, 多段发送的时候, 表示本报文段所发送的数据的第一个字节的序号
4. 确认号 acknowledgment number: 占 4 个字节, 表示期望收到对方下一个报文段的序号值。
5. 数据偏移 offset: 0.5 个字节, 表示TCP 报文段的首部长度
6. 保留 reserved: 占 0.75 个字节 (6 位)。保留为今后使用，但目前应置为 0。
7. 标志位 tcp flags: 一共有 6 个，分别占 1 位，共 6 位 , 每一位的值只有 0 和 1. ACK(Acknowlegemt), SYN(Synchronization), FIN(Finis) 都在这里
8. 窗口大小 window size: 占 2 字节, 表示现在允许对方发送的数据量, 这样对方就可以控制发送数据的速度
9. 检验和 checksum: 占 2 个字节, 由发送端填充，接收端对 TCP 报文段执行 CRC 算法校验
10. 紧急指针 urgent pointer: 占 2 个字节。
11. 选项 tcp options

## 三次握手

- 第一次握手: 

  客户端发送报文: SYN=1, Sequence Number=x, 进入 SYN_SEND 状态

- 第二次握手: 

  服务器收到报文, 发送: SYN=1, ACK=1, Sequence Number=y, Acknowledgemt Number=x+1, 进入 SYN_RCVD 状态

- 第三次握手: 

  客户端收到报文, 发送: SYN=0, ACK=1, Sequence Number=x+1,Acknowledgemt Number=y+1, 进入 ESTABLISHED 状态.

  第三次握手会携带数据, 

生成 Sequence Number 是为了防止滞后报文影响连接

## 四次挥手

- 第一次挥手: 

  客户端发送报文: FIN=1, Sequence Number = M, 进入FIN_WAIT_1状态

- 第二次挥手: 

  服务端收到报文, 发送报文: ACK=1, Sequence Number = M + 1, 进入CLOSE_WAIT状态; 

  客户端收到后, 进入FIN_WAIT_2状态

- 第三次挥手: 

  服务端发送报文: FIN = 1, Sequence Number = N, 进入LAST_ACK状态

- 第四次挥手: 

  客户端收到报文, 发送报文: ACK = 1, Sequence Number = N + 1, 进入TIME_WAIT状态, 经过 2MSL 之后，自动进入 CLOSED状态; 

  服务端收到报文, 进入CLOSED状态.

## 参考

https://jerryc8080.gitbooks.io/understand-tcp-and-udp/chapter2.html

https://lotabout.me/2019/TCP-connection-establish-and-termination/

