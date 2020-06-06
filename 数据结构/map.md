# map

### hash 函数

常用的 hash 函数

SHA-1，SHA-256，SHA-512，MD5

hash碰撞解决策略

1. 开放地址

   策略: 依次查找 h0, h1, h2, ...., 其中, M 最好是奇素数

   线性探查法:  hi = (hash(key) + i) mod M
   平法探查法:  hi = (hash(key) + i*i) mod M
   双 hash探测: hi = (h1(key) + i * h2(key)) mod M

2. 链表

### 扩容策略

redis 触发rehash, 会分多次, 渐进完成旧表到新表的复制.

### 红黑树优化

java HashMap 在冲突节点链表长度>=8 会转成红黑树, <6 个退化成链表

### go map

go 使用 memhash(16 位), 将哈希值分为 高位哈希和低位哈希,  低位 hash决定桶, 高位 hash 决定桶里面的位置, 每个桶长度 8.

负载因子 6.5, 计算方式: count / 2 ** B , 2**B 表示桶的最大数量



### 问题

map 实现太复杂了, 有时间慢慢看吧

