# sync.Map

包含 read, dirty, 都是使用 map 存储数据

读的时候, 会先读 read, 如果没有, 则 miss +1, 当miss>len(dirty), 就将dirty 提升为 read.

dirty 会加锁



具体太复杂, 有时间慢慢看

