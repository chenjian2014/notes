# python

### 迭代器与生成器
- 可迭代对象 Iterable
  实现方法 `__iter__`

- 迭代器 Iterator
  实现方法 `__iter__` 和 `__next__`

  `__next__`介绍应当抛出来 StopIteration 异常

  迭代器可以用 for 循环
  for循环 首先调用`__iter__`获取迭代器对象, 然后调用 `__next__`

- 判断
  from collections import Iterable, Iterator

- 生成器

  使用 yield 定义的函数就是一个 generator

  生成器会挂起当前函数

### yield

yield 会挂起当前函数

yield 可以接收 send 调用的结果

```python
def f():
    a = 1
    while a < 100:
        y = yield a
        a += y
y = f()  # y 就是一个 generator
next(y)  # 使代码运行到 yield 挂起, 也可用 y.send(None) 代替
y.send(10)  # 发送数据给 y
```

### asyncio

使用 async def 定义协程函数 f, 执 f(), 则得到 coroutine, 即协程对象

协程对象本身没有运行, 在 asyncio.create_task(a) 之后则会触发运行, 并生成 Task(继承自 Future)

Future 是占位符, 表示最终结果

默认 _UnixSelectorEventLoop

### WSGI

Web 服务器网关接口, 是一个规范, 规定 web 框架如何与 web server 通信

实现 WSGI 的web 框架有: flask, django 等

实现 WSGI 协议的 server 有 uWSGI, gunicorn

协议内容:

server: 负责从客户端接收请求, 将 request 转发给 application; 将 application的 response 返回给客户端

application: 接收 server 的 request, 并反馈 application . application接收 environ 和 start_response 参数

### 垃圾回收机制

引用计数: 循环引用问题
标记清除: 标记阶段, STW, 从根节点开始扫描(调用栈, 全局对象, 寄存器), 扫描完成之后, 不可达对象放入不可达链表, 清除. 问题: 内存碎片化
分代: 三代. 一般青年代 复制算法(两块内存区, 从一块复制到另一块), 后面 标记清除或者标记压缩, 老年代可能一直存活.

### new 和 init 的区别

new 创建实例, 有返回值.

init 初始化实例

### 多进程, 多线程, 协程

