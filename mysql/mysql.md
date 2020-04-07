# MYSQL总结

### 索引

- 原理

  二叉树, 二叉查找树, 平衡二叉树, 红黑树, B 树, B+树

- 分类

  聚簇索引, 二级索引

- 二级索引分为普通索引, 唯一索引, 全文索引

- 联合索引

- 最左前缀匹配

- 覆盖索引

- 回表

- hash 索引与 B+树索引

- 索引条件下推

- 页分裂

- 页合并

- 前缀索引

### 锁

- 表锁
- 行锁
- 共享锁, 读锁
- 排他锁, 写锁
- 意向共享锁
- 意向排他锁
- 乐观锁
- 悲观锁
- 间隙锁, next-key 锁
- 记录锁
- 临键锁
- 死锁

### 分库分表

- 垂直分表
- 水平分表
- uuid
- Snowflake 雪花算法
- 分片策略: id 取模, 一致性 hash 算法
- 扩容: 停机扩容, 平滑扩容
- 同步: 主从同步, 主主同步
- binlog
- 高可用方案
- 分区

### 优化

- 慢查询   https://www.cnblogs.com/sharpest/p/10390035.html
- explain

### 事务

- 原子性(Atomicity)
- 一致性(Consistency)
- 隔离性(Isolation)
- 持久性(Durability)
- 未提交读: read uncommitted
- 已提交读: read committed
- 可重复读: repeatable read
- 串行化: serializable
- 脏读
- 不可重复读
- 幻读
- 保存点

### 事务实现原理

- redo
- undo
- mvcc

### 分布式事务

- XA协议
- 二阶段
- 三阶段

- seata