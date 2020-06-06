# sync

### Once

多次调用 函数 f, once.Do(f) 只会在第一次执行

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var once sync.Once
	onceBody := func() {
		fmt.Println("Only once")
	}
	for i := 0; i < 10; i++ {
		once.Do(onceBody)
	}
}

```

### WaitGroup

WaitGroup 用来等待一组 goroutine 结束，操作流程为：

1. 主 goroutine 调用 `Add()` 设置需要等待结束的 goroutine 的数量
2. 主 goroutine 调用 `Wait()` 开始阻塞直到所有 goroutine 运行结束
3. 每个 goroutine 开始运行并在结束的时候调用 `Done()` 

其代码逻辑是: 每次启动一个 goroutine, 就 Add 1, 然后当 goroutine 中 done 一次, 即 Add -1, wait 会等待直到内部的counter 为0.

```go
func main() {
	var wg sync.WaitGroup
	var urls = []string{
		"www.qq.com",
		"www.baidu.com",
		"www.taobao.com",
	}
	for _, url := range urls {
		// 每次启动一个 goroutine 来做 http GET 动作，每启动一个就调用一次 Add()
		wg.Add(1)
		go func(url string) {
			// 一般会将 Done() 放在 defer 中
			defer wg.Done()
			http.Get(url)
		}(url)
	}

	// 主 goroutine 开始等待所有 goroutine 结束
	wg.Wait()
}
```

### Pool

Pool 是一个临时对象保存（`Put()`）和获取（`Get()`）的集合，可安全地并发访问。

常用应用场景: 函数内每次申请 slice, 销毁后下次调用又重建, 引起 GC. 采用 Pool 后, 就不用每次申请内存

注意: 适用与无状态的对象的复用，而不适用与如连接池之类的

```go
var bufPool = sync.Pool{
	New: func() interface{} {
		return new(bytes.Buffer)
	},
}
```

参考: https://blog.cyeam.com/golang/2017/02/08/go-optimize-slice-pool

### 互斥锁 Mutex

```go
func (m *Mutex) Lock()
func (m *Mutex) Unlock()
```

### 读写锁 RWMutex

由于这里需要区分读写锁定，我们这样定义：

- 读锁定（RLock），对读操作进行锁定
- 读解锁（RUnlock），对读锁定进行解锁
- 写锁定（Lock），对写操作进行锁定
- 写解锁（Unlock），对写锁定进行解锁

如何理解读写锁呢？

1. 同时只能有一个 goroutine 能够获得写锁定。
2. 同时可以有任意多个 gorouinte 获得读锁定。
3. 同时只能存在写锁定或读锁定（读和写互斥）。

也就是说，当有一个 goroutine 获得写锁定，其它无论是读锁定还是写锁定都将阻塞直到写解锁；当有一个 goroutine 获得读锁定，其它读锁定任然可以继续；当有一个或任意多个读锁定，写锁定将等待**所有**读锁定解锁之后才能够进行写锁定。所以说这里的读锁定（RLock）目的其实是告诉写锁定：有很多人正在读取数据，你给我站一边去，等它们读（读解锁）完你再来写（写锁定）。

### Cond 条件变量

```go
func main() {
	ch := make(chan interface{}, 5)

	// 新建 cond
	var l sync.Mutex
	var condition bool

	cond := sync.NewCond(&l)

	for i := 0; i < 5; i++ {
		go func(i int) {
			defer func() {
				ch <- nil
			}()

			func () {
				// 争抢互斥锁的锁定
				cond.L.Lock()
				// 解锁后, 才能并发执行任务
				defer cond.L.Unlock()

				// 注意这里判断条件要用 for
				// 由于加锁, 所有 goroutine 的条件判断都是顺序执行的
				for !condition {
					cond.Wait()
					fmt.Printf("goroutine%d: 收到一个通知\n", i)
				}
			} ()
			// 并发执行任务
			fmt.Printf("goroutine%d: 执行任务\n", i)
		}(i)
	}

	// 确保所有 goroutine 启动完成
  // 这里应该怎么显式的等待完成而不是随便指定给一个等待时间
	time.Sleep(time.Millisecond * 20)

	fmt.Println("broadcast...")
	condition = true
	cond.Broadcast()

	for i := 0; i < 5; i++ {
		<-ch
	}
}
```

### atomic.Value

有 load 和 store 操作

### 参考

https://deepzz.com/post/golang-sync-package-usage.html
