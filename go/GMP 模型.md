# GMP 模型

参考: https://studygolang.com/articles/11627

https://www.cnblogs.com/xc1234/p/8995698.html

M: 代表内核线程

G: 代表 goroutine

P: Processor, 处理器, 维护一个 G 的队列, 负责执行G. 可以通过 runtime.GOMAXPROCS 设置.

​	默认值是 cpu 核数

p 队列: 分为本地队列和全局队列

抢占式调度: 一个 G 最多占用 CPU 10ms, 然后被其他 G 占用

m0: 启动程序后的主线程, 负责执行初始化操作和启动第一个g

g0: 仅用于负责调度的G, g0不指向任何可执行的函数, 每个m都会有一个自己的g0

过程:

首先, 创建 M0 和 G0, 关联 M0 和 G0, 然后, 

程序根据 GOMACPROCS 创建 P, 然后新的 G 会加入 P 的本地队列, 本地队列满了加入 P 的全局队列. 然后 P 查找空闲的 M, 如果 M 不存在, 或者阻塞, 就创建新的M.绑定之后, M 从 P 的本地队列随机获取 G 执行. 如果队列为空, 就从其他的 P 拿一半的 G. 如果从其他 P 拿不到, 就 从全局队列拿. 一个 G 最多占用 CPU 10ms, 然后让出.

