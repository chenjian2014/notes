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
