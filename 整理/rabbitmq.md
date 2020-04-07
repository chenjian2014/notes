# rabbitmq

交换器 exchange
服务节点 broker
队列 queue: 应该事先创建好, 而不是使用时声明, 尤其是消费者
信道 channel: 一个 connection 可以有多个 channel, 但是 channel 不是并发安全的, 不能线程间共享


生产者过程:
    声明 exchange
    声明 queue
    队列绑定 exchange和 binding_key, 一个 exchange 可以绑定多个 queue
    发送消息: 指定 exchange, routing_key, message

消费者过程:
    注意: ack 之后, 消息会被删除
    声明 queue
    队列绑定
    给定消费回调函数
    basic_consume
    start_consuming: 会阻塞, 相当于一个 while true


交换器类型:
    注意: 可以多个队列有相同 binding_key. 每个队列有相同的数据
    fanout: 消息路由到所有绑定队列
    direct: 消息路由到routing_key 与 binding_key 完全一致的队列. 
    topic: 模式匹配: 示例: binding_key=com.* , 则 routing_key=com.abd 会被路由到该 bingding_key 对应的 queue
    headers: 已废除


推拉模式:
    应该是用推模式
    拉模式会阻塞 channel

消费者ack:
    autoAck=true, 消息发送出去就自动删除, 有丢失风险

消费者对消息的处理:
    1. ack
    2. reject
    3. 连接断开


publish 可靠性保证:
    如果没有对应 binding_key, 则:
        1. 可以设置 mandatory, 监听回调函数, 投递失败会返回回来
        2. 或者设置备份交换器, 投递失败则存入备份交换器绑定的队列
    事务机制:
        tx_select 与 tx_commit, tx_rollback, 但是性能很低
    发送方确认机制:
        将信道设置成 confirm 模式, rabbitmq 返回唯一消息 id
        分成同步 comfirm 和异步 confirm

消费端可靠性保证:
    手动 ack

消息传输保证:
    最少一次 需要:
        生产者开启事务或者 confirm 模式, 进行持久化, 并且需要开启 mandatory 或者设置备份交换器
        消费者需要手动 ack
    无法保证恰好一次:
        kafka 也无法保证.
        场景: 消费者发送 ack, 但是断网导致消费者没有收到, 下次重连 connection 则重复消费. 但是这个应该可以消费者保证, 比如先发 ack, 再 commit mysql.
        场景: 生产者发送完, rabbitmq 返回, 断网导致生产者没有收到, 则重复发送.
        方案: 保证消费消息的幂等行.

虚拟主机 vhost


过期时间 ttl:
    可对队列或者每条消息设置

死信队列:
    消息被拒绝
    消息过期
    队列到最大长度

延迟队列:
    正常队列设置过期时间, 过期后放到了死信队列, 消费者消费死信队列, 即可实现延迟队列功能
    redis 可以用 sorted set 实现

优先队列:
    可以设置优先级

持久化:
    交换器持久化: 不持久化, 关闭 server 就丢失 交换器元数据
    队列持久化: 不持久化, 关闭 server 就丢失数据
    消息持久化: 刷盘有延迟

