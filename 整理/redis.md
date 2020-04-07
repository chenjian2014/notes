# redis

布隆过滤器:
    利用布隆过滤器减少磁盘 IO 或者网络请求, 因为一个值必定不存在, 不用去查数据库
    使用 bitmap 的 setbit getbit

HyperLogLog:
    统计去重数量

订阅消息:
    subscribe, publish channel

持久化:
    快照 + AOF, bgsave 另外 fork 子进程生成快照, 因为 fork 会自动复制数据

数据结构:
    字符串: 长度和数组, 扩容增加一倍, 类似 ArrayList
    list: 链表
    hash: 数组 + 链表, rehash: 超过负载因子范围则 rehash, 扩展2倍 , 渐进 rehash, 增删改同时修改两个数组, 新增新数组
    跳跃表: 链表, 每个节点存多个指针, 指向多个跨度不同的节点. 比平衡树实现简单. zset 同时使用了跳跃表+hash

LRU 算法:
    使用有序 dict, dict 里面存的是 Entry + 前后指针, 例如 python collections.OrderedDict

过期键删除策略:
    惰性删除 + 定期删除: 在过期集合中, 随机 20 key, 如果过期超过 1/4, 再次随机 20

内存满后数据淘汰策略:
    默认 noeviction, 报错
    volatile-lru, 删除 使用最少的 设置了过期时间的 key, 即[过期集合]中的 key
    allkeys-lru: 所有 key lru
    volatile-ttl: 按 ttl 删

连接:
    IO 多路复用 + epoll

事务:
    watch + multi + exec

哨兵模式 sentinel:
    一般由 3 个 sentinel 组成集群
    主节点失效, 会将从节点升级为主节点
    每 1s 一次 PING
    主观下线与客观下线
    一个 sentinel 判断 master 主观下线后, 询问其他 sentinel, 过半数则判断 客观下线, sentinel 会选取 leader 执行故障转移操作

保证 mysql+redis 数据一致性:
    强一致性数据直接读 mysql 比较好
    前后双删不可取
    先写 mysql, 再删 redis, 可以再做一个进程监控 binlog, 再次删 redis
    如果主从, 则需要订阅从库的 binlog

一致 hash 算法:
    场景: 数据 hash 到四台机器上
    算法: 计算机器 hash, 分别是 {10, 20, 30, 40}, 再计算数据 hash, 看 hash 再哪个区间即可. 如果机器少, 则加虚拟节点.
    好处: 一台机器崩溃, 迁移数据量最少

redis集群:
    CRC16(key) % 16384 来计算键 key
    客户端访问任一master, 如果数据不在本 master, 则给客户端返回对应的 master
    如果 master 下线, 集群会进行选举, 选举与 sentinel 类似, 所以 redis 集群不需要配置 sentinel 模式

扩容方案:
    虚拟机: 直接增加内存
    物理机: 直接向 redis 集群增加节点

分布式锁:
    数据库: 利用 unique key 的增加和删除, 超时根据 inserttime 判断. 缺点是性能低, 需要轮询
    redis: set a 100 ex 10 nx, 设置 10s 过期, 不存在才能设置成功. 缺点需要轮询, 性能稍好. 
        删除需要用 lua 脚本, 根据标识判断删除. 原因: A 加锁, 超时; 然后 B 加锁, 这时A 去解锁, 删掉了 B 的锁. 方案是锁里加标识符, 这样 A 就不会删掉 B 的锁.
    zookeeper: 实现复杂, 性能低, 一般不采用

查询指定模式key:
    scan cursor MATCH pattern: 返回下次迭代的 cursor(0 表示完成), 以及数据. 复杂度O(n)
    缺点是 可能重复, 新增数据扫不到, 优点是不阻塞

异步队列:
    list: lpush, rpop, brpop
    pub/sub: 一次生产, 多次消费, 但是消费断掉会丢消息

延时队列:
    用 sorted set 实现, zadd, zrange

缓存穿透:
    查询的数据不存在, 每次都从数据库查. 解决方案是 布隆过滤器

缓存雪崩:
    设置过期时间, 同一时间过期, 导致大量查询 db. 大量加载的时候, 过期时间加随机值.

缓存击穿:
    热点 key, 比如 秒杀商品, 一旦过期, 会导致并发访问db. 应该不设置过期, 或者加分布式锁 load db.


