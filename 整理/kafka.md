# kafka


发送方式:
    send: get则阻塞等待, 是同步调用; 传入 callback 则异步发送
    acks机制: 0: 发送不等待, 1: leader 收到就会响应, all: 所有从节点全部收到消息才响应

消费者提交方式:
    自动提交: 批量拉取消息, 如果消费者崩溃, 会导致重复消费
    同步提交: 调用 consumer.commitSync, 提交最新的偏移量, 可传入 partition 和 offset, 这样能避免重复消费
    异步提交: consumer.commitAsync, 消费端发送后不等待
    控制偏移量: 保存 + offset 通过事务都保存到 mysql, 然后再其他消费者触发 rebalance 的时候, 读取偏移量. 这样可以更可靠一点

exactly-once:
    kafka 不支持, 但是可以考虑在消息中加入唯一键, 在消费时处理. 即保证消息消费的幂等性

副本:
    每个分区都有多个副本, 分为 leader replica 和 follower replica, 只有 leader 才能进行读写
    如果 follower 长时间(10s)没有请求数据或没有请求最新数据, 则认为不同步
    每个分区都有 leader replica.

控制器controller:
    broker 会在 zookeeper 注册成为控制器, 负责分区 leader副本 的选举

选举:
    控制器选举: 借助 zookeeper
    分区 leader副本 选举: 由控制器选择
    消费者leader 选举.

零复制:
    

分区分配:
    1. 在 broker 建平均分配分区副本
    2. 首领副本分散在不同 broker
    采用轮询分配

片段segment:
    每个分区包含多个片段, 删除以片段为单位.


索引:
    每个分区有一个索引, 用于快速定位 offset


可靠性保证:
    同一个分区消息有序
    消费者只能读取以提交消息
    只要有副本活跃, 已经提交的消息就不会丢失

最少同步副本:
    当同步副本少于该值, 分区不可用