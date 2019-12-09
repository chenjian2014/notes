# \_\_new\_\_和单例

<p align="right">2019-11-13</p>

```python
import time
from datetime import datetime
from multiprocessing.dummy import Pool


class A:
    instance = None

    def __new__(cls, *args, **kwargs):
        time.sleep(3)

        if cls.instance is None:
            print(datetime.now())
            cls.instance = super().__new__(cls, *args, **kwargs)
        return cls.instance


def worker(x):
    return A()

pool = Pool(10)
data = pool.map(worker, [1,2,3,4,5])
pool.close()
pool.join()


```

多次执行, 可能出现如下结果

```python
2019-11-13 18:42:11.719025
2019-11-13 18:42:11.719154
2019-11-13 18:42:11.719279
2019-11-13 18:42:11.719233
2019-11-13 18:42:11.719409

In [27]: data
Out[27]:
[<__main__.A at 0x1106097b8>,
 <__main__.A at 0x1100e7d30>,
 <__main__.A at 0x110638da0>,
 <__main__.A at 0x1106380f0>,
 <__main__.A at 0x110638a90>]
```

说明每次执行 A 类的初始化, 都会执行 _\_new\_\_方法, 也就是说, 网上所说的关于 _\_new\_\_ 实现 python 单例模式是错误的.

例外, 当启用多进程的时候, 会出现如下情况

```python
import time
import os
from datetime import datetime
from multiprocessing import Pool


class A:
    instance = None

    def __new__(cls, *args, **kwargs):
        time.sleep(3)

        print(datetime.now().strftime('%Y-%m-%d %H:%M:%S') + " " + str(os.getpid()))

        if cls.instance is None:
            cls.instance = super().__new__(cls, *args, **kwargs)
        return cls.instance


def worker(x):
    return A()

print("主进程 id: " + str(os.getpid()))
pool = Pool(10)
data = pool.map(worker, [1,2,3,4,5])
pool.close()
pool.join()

结果如下:
主进程 id: 23951
2019-11-13 18:47:36 24962
2019-11-13 18:47:36 24963
2019-11-13 18:47:36 24965
2019-11-13 18:47:36 24966
2019-11-13 18:47:36 24964
2019-11-13 18:47:39 23951
2019-11-13 18:47:42 23951
2019-11-13 18:47:45 23951
2019-11-13 18:47:48 23951
2019-11-13 18:47:51 23951
```

匪夷所思, 猜测情况是: 在子进程实例化后, 序列化对象, 并将数据传给主进程, 主进程在反序列化的时候会调用_\_new\_\_ 方法.



## 总结

1. _\_new\_\_ 无法用于实现单例对象, 因为每次实例化对象都会调用 _\_new\_\_, 并且没有加锁, 是并发调用的

2. _\_new\_\_ 的主要作用是创建对象, \_\_init\_\_主要用于初始化示例