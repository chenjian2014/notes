# go

### init 函数
同一个包和同一个文件都允许多个 init 函数存在.
同一个包 init 函数, 经实验, 按照文件的字典序加载, 同一个文件按照先后顺序加载
不同包, 按照包的导入顺序加载
但是, 官方建议不要依赖 init 的执行顺序

### slice

```go
type slice struct {
	array unsafe.Pointer
	len   int
	cap   int
}
makeslice 通过 make() 创建 slice 实例, 调用 mallocgc 分配内存. 小对象分配在 P 的缓存队列, 
大对象分配到堆上.

growslice 增加 slice, 小于 1024 两倍增长, 否则 1.25 倍增长
```

### map

```go
type hmap struct {
	B         uint8 
	noverflow uint16 
	buckets    unsafe.Pointer // array of 2^B Buckets. may be nil if count==0.
}
type bmap struct {
	tophash [bucketCnt]uint8  // 长度 8, 记录hash 值的高 8 位
}
tophash存储内容: key0...key7, val0...val7, overflow 指针

map 存储: 
	计算 key 的 hash 值,
	低 8 位从 hmap.buckers 找到 bmap, 
	高 8 位 从 bmap.tophash 找到 对应的 kv, 
  如果 tophash 没有满, 直接写入, 如果已满, 则新分配的 bucket 挂在 overflow 上.
  当 factor=13/2, 即 总数量/bucket=6.5,  或者 overflow 太多(>2**B)则扩容, 每次扩容, buckets 翻倍.
	扩容逐步进行, 一次迁移一个 bucket
  删除只会标记, 实际上内存不会减少
makemap: 初始化
mapaccess: 查找
mapassign: 写入
mapdelete: 删除
```

### sync.map

```go
type Map struct {
	mu Mutex
	read atomic.Value // readOnly
	dirty map[interface{}]*entry
	misses int
}
Load: 查询, 首先查 read, 如果查不到, 加锁, 从 dirty 查, misses++, 如果 misses==dirty 数量, dirty 变为 read.
Store: 元素不存在, 先存储在 dirty 中. 如果在 read 中, 用 CAS 直接修改, 否则加锁, 然后在dirty 中则直接修改, dirty 为空还需要从 read 全部复制过来. 这样保证了 dirty 要么包含 read, 要么为空

用空间换时间, 同时大量使用 atomic 和锁保证操作的原子性
```

### channel

```go
type hchan struct {
	buf      unsafe.Pointer // points to an array of dataqsiz elements
	sendx    uint   // send index
	recvx    uint   // receive index
	recvq    waitq  // list of recv waiters
	sendq    waitq  // list of send waiters
}
type waitq struct {
	first *sudog   // sudog 表示 g, 是链表, 含有 next 节点
	last  *sudog
}

chansend: 发送数据
chanrecv: 接收数据
主要流程: 从 send g 复制数据到 buf, 然后再复制到 recv g
```

chansed 流程:

整个流程如下：

1. 检查recvq是否为空，如果不为空，则从recvq头部取一个goroutine，将数据发送过去，并唤醒对应的goroutine即可。

2. 如果recvq为空，则将数据放入到buffer中。

3. 如果buffer已满，则将要发送的数据和当前goroutine打包成`sudog`对象放入到`sendq`中。并将当前goroutine置为waiting状态。

chanrecv 流程:

如果 sendq 为空, 直接把数据从 buffer 复制到 receive g.

buffer中队首的数据拷贝给了对应的接收变量，同时将sendq中的元素拷贝到了队尾，这样可以才可以做到数据的FIFO(先入先出)

### new 和 make

new: 接受类型参数, 返回内存地址, 同时将值置为 0 值. 所以 new 返回的都是指针

make: 只能用于 chan, map, slice 的创建, 分别调用 makeslice, makemap, makechan

### select

```go
type scase struct {
	c           *hchan         // chan
	elem        unsafe.Pointer // data element
	kind        uint16
	pc          uintptr // race pc (for race detector / msan)
	releasetime int64
}
selectgo: 针对 []scase, 打乱顺序, 锁住, 遍历 channel, 发现可读可写, 则解锁执行.
```

### gin

核心: engine, pool, context

```go
type Engine struct {
	RouterGroup
	pool             sync.Pool    // 存储 Context
	trees            methodTrees  // []methodTree, 包含 method 和包含 path 的 tree
}

type RouterGroup struct {
	Handlers HandlersChain // []HandlerFunc, func(*Context). 中间件即HandlerFunc
	engine   *Engine
}

type Context struct {
	Request   *http.Request   // http 请求, 直接从 http.ListenAndServe 获得
	Writer    ResponseWriter  // http 响应
	Keys map[string]interface{}
}

```

### sync

```go
	sync.Pool
	sync.WaitGroup
	sync.Cond
	sync.Map
	sync.Once
	sync.Mutex
	sync.RWMutex
  atomic.Value
```

### GMP

```go
func newproc(siz int32, fn *funcval) // 创建 g, 加入 g 队列

```



### context

### http

### db

### 数据竞态检测 race

go -race, 对应 raceenabled 变量

