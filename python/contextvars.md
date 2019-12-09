# contextvars


```python
import contextvars
import time
from multiprocessing.dummy import Pool

var = contextvars.ContextVar('var')
const = None  # 对照组


def f():
    time.sleep(1)
    data = var.get()
    return data, const


def s(x):
    time.sleep(1)
    global const
    const = x

    var.set(x)
    d = f()
    print(x, d)


pool = Pool(10)
pool.map(s, range(10))
pool.close()
pool.join()
```

结果如下
```
0 (0, 1)
3 (3, 1)
4 (4, 1)
7 (7, 1)
9 (9, 1)
6 (6, 1)
5 (5, 1)
1 (1, 1)
8 (8, 1)
2 (2, 1)
```

