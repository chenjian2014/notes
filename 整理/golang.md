# golang

### channel

```go
// 声明
ch := make(chan int)
// 带缓冲区
ch := make(chan int, 100)
// 发送
ch <- x
// 接收
y := <- ch
// 关闭
close(ch)
// 判断 channel 是否关闭
x, ok := <- ch  // ok=false 表示 channel 已经关闭
// 只写 channel
func send(x <-chan int)
// 只读 channel
func receive(x chan<- int)
```

### close channel

1. 重复关闭 channel 会 panic
2. 已经关闭的 channel, 可读不可写. 但是需要判断 channel 是否关闭, 如果已经关闭, 得到的是零值和 false

### 有缓存 channel 与无缓冲 channel 区别

```go
// 无缓冲 channel 发送接收同时发生, 下面示例会 panic, 显示 send on closed channel
// 但是有缓存 channel 不会出现此类 panic
func main() {
	c := make(chan int)
	go func() {c <- 1}()
	time.Sleep(time.Second)
	close(c)   // 此处 close 实际发送在 c <- 1 之前
	x := <- c
	fmt.Println(x)
}
```

### select

空 select 或者 channel 没有初始化, 会导致 select 阻塞

如果 case 有多个同时满足, 随机执行一个

如果 default 外其他 case 都不满足, 则立即执行 default, 不会有阻塞

如果没有 default, 会一致阻塞直到满足为止

### context

```
用来控制 goroutine, 一般和 select 配合, 判断 goroutine 是否被 cancel 或者超时
context.WithValue
context.WithTimeout
context.WithCancel
context.WithDeadline
```

### GMP 模型

参考: https://studygolang.com/articles/11627

M: 代表内核线程

G: 代表 goroutine

P: Processor, 处理器, 维护一个 G 的队列, 负责执行G. p 代表真正的并行度, 可以通过 runtime.GOMAXPROCS 设置.

​	默认值是 cpu 核数

p 队列: 分为本地队列和全局队列

抢占式调度: 一个 G 最多占用 CPU 10ms, 然后被其他 G 占用

m0: 启动程序后的主线程, 负责执行初始化操作和启动第一个g

g0: 仅用于负责调度的G, g0不指向任何可执行的函数, 每个m都会有一个自己的g0

过程:

首先, 创建 M0 和 G0, 关联 M0 和 G0, 然后, 

程序根据 GOMACPROCS 创建 P, 然后新的 G 会加入 P 的本地队列, 本地队列满了加入 P 的全局队列. 然后 P 查找空闲的 M, 如果 M 不存在, 或者阻塞, 就创建新的M.绑定之后, M 从 P 的本地队列随机获取 G 执行. 如果队列为空, 就从其他的 P 拿一半的 G. 如果从其他 P 拿不到, 就 从全局队列拿. 一个 G 最多占用 CPU 10ms, 然后让出.



## 垃圾回收

垃圾回收算法
1. 引用计数法
    原理: 任何对对象 A 的引用都会使A 的引用计数+1; 引用对象被删除则-1; 为 0 则回收掉
    优点: 实时性高
    缺点: 每次都要更新, 无法解决循环引用的问题

2. 标记清除
    标记: 从根节点开始标记对象
    清除: 未被标记的对象需要被清除
    缺点: STW, 内存碎片化严重

3. 标记压缩
    标记: 从根节点开始标记对象
    压缩: 将标记对象压缩到内存一端, 清理边界外对象
    优点: 解决内存碎片化问题

4. 复制算法
    将内存分为两块, 每次只用其中一块, 垃圾回收时, 将存活对象复制到另一块
    缺点: 适用于垃圾较多的情况, 内存利用率低
    与标记压缩区别: 
        标记压缩因为有标记阶段, 所以耗时多, 但是只需要一块内存
        复制算法没有单独标记的阶段, 所以耗时少, 但是耗内存高
    标记压缩: 时间换空间
    复制算法: 空间换时间

5. 分代
    jvm 分为年轻代和老年代, 年轻代用复制算法, 老年代用标记清除或压缩


golang 垃圾回收

根对象:
    是垃圾回收器最先检查的对象, 包括:
    全局变量, 执行栈, 寄存器

goland 的GC 是 无分代, 不整理, 并发的三色标记清除算法

三色标记法
    白色对象: 未被回收器访问到的对象
    灰色对象: 正被回收器检查的对象
    黑色对象: 已扫描完成的对象, 确认存活

写屏障:
    Dijkstra 插入屏障和 Yuasa 删除屏障

gc 细节:
    标记准备阶段, 开启写屏障, STW
    扫描标记阶段, 写屏障开启, 并发操作
    标记终止阶段, 关闭写屏障, STW
    内存清扫阶段, 内存归还到堆, 写屏障关闭, 并发操作
    内存归还阶段, 内存归还操作系统, 写屏障关闭, 并发操作

触发 gc:
    主动触发: runtime.GC
    被动触发: 
        超过两分钟没有 GC, 强制 GC;
        超过内存阈值



