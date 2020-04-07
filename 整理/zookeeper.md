# zookeeper

持久节点和临时节点:
    临时节点超时或者客户端主动关闭会被删掉

有序节点:

脑裂:
    大多数有效能避免脑裂

主从模式

数据节点znode

集群角色:
    leader, follower, observer
    observer: 不参与选举和过半写成功


zab 协议(zookeeper atomic broadcast):
    模式:
        崩溃恢复, 消息广播
    事务 ZXID:
        低 32 位: 由 leader +1递增
        高 32 位(epoch): 每次选举则由新leader的ZXID的高32位 加1

    三个阶段:
        选举, 同步, 广播


写只有 leader, 读都可以, 但是 zookeeper 不保证读的是最新数据


羊群效应:
    大量客户端 watch 一个znode, 导致收到大量通知. 
    例如分布式锁, 大量客户端 watch 一个 znode, 修改方案: 创建有序节点, 获取所有子节点, 如果自己的节点最小, 则获得锁, 否则, 监听比自己小的节点.


paxos 协议

zab 协议

raft 协议



