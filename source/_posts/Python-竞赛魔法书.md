# 输入
## 输入单个变量
Python 默认读入是字符串.
``` python
s = input()
```

若想转换成整数, 最简单的办法便是"强转".
``` python
n = int(input())
```

当然也可以读入一些其他类型, 比如说浮点.
``` python
n = float(input())
```

或者是十进制小数.
``` python
from decimal import Decimal

n = Decimal(input())
```

**注, 请不用使用 eval , 那会超级慢!**

## 输入多个变量
解包, 是一种将可迭代对象中的元素分成单个变量的技巧. 利用这一点我们可以简洁的读入一行多个值.
``` python
# map 并不对应 cpp 的树形映射表.
# 在 python 中, map 将给定的函数依次应用于迭代器的每一个元素, 并返回计算结果.
a, b, c = map(int, input().split()) # 注意, split默认以空白字符分割.
```

相同的, 输入也可以是其他类型, 比如浮点.
``` python
a, b, c = map(float, input().split())
```

## 输入一行列表
既然读入后返回的是一个迭代器 (生成器), 那我们也可以简单的转换成列表.
``` python
lst = list(map(int, input().split()))
```

当然了, 输入也可以是其他类型.
``` python
lst = list(map(float, input().split()))
```

或者是, 当需要拆位输入的时候.
``` python
lst = list(map(int, input()))
```

## 混合输入
可能有一些题目会要求输入多种不同的操作.
``` python
cmd, *args = map(int, input().split()) # args读进来是list.
```

或者是, 一行, 第一个整数 $n$ , 后面跟 $n$ 个整数.
``` python
_, *lst = map(int, input().split()) # args读进来是list.
```

## 快读
快读小板子.
``` python
import sys

input = sys.stdin.readline
```

快读大板子
``` python
import sys
import os
from io import BytesIO, IOBase

BUFSIZE = 8192


class FastIO(IOBase):
    newlines = 0

    def __init__(self, file):
        self._fd = file.fileno()
        self.buffer = BytesIO()
        self.writable = "x" in file.mode or "r" not in file.mode
        self.write = self.buffer.write if self.writable else None

    def read(self):
        while True:
            b = os.read(self._fd, max(os.fstat(self._fd).st_size, BUFSIZE))
            if not b:
                break
            ptr = self.buffer.tell()
            self.buffer.seek(0, 2), self.buffer.write(b), self.buffer.seek(ptr)
        self.newlines = 0
        return self.buffer.read()

    def readline(self):
        while self.newlines == 0:
            b = os.read(self._fd, max(os.fstat(self._fd).st_size, BUFSIZE))
            self.newlines = b.count(b"\n") + (not b)
            ptr = self.buffer.tell()
            self.buffer.seek(0, 2), self.buffer.write(b), self.buffer.seek(ptr)
        self.newlines -= 1
        return self.buffer.readline()

    def flush(self):
        if self.writable:
            os.write(self._fd, self.buffer.getvalue())
            self.buffer.truncate(0), self.buffer.seek(0)


class IOWrapper(IOBase):
    def __init__(self, file):
        self.buffer = FastIO(file)
        self.flush = self.buffer.flush
        self.writable = self.buffer.writable
        self.write = lambda s: self.buffer.write(s.encode("ascii"))
        self.read = lambda: self.buffer.read().decode("ascii")
        self.readline = lambda: self.buffer.readline().decode("ascii")


sys.stdin, sys.stdout = IOWrapper(sys.stdin), IOWrapper(sys.stdout)
input = lambda: sys.stdin.readline().rstrip()
```

# 输出
## 输出一行列表
依靠手动解包, 我们可以很简洁的输出列表
``` python
print(*lst)
```

假如需要换行.
``` python
print(*lst, sep="\n")
```

# 列表和迭代器
## 初始化
这些, 可能并不是魔法, 而是一些坑点.
``` python
lst = [0] * 10  # 正确的

lst = [[0, 1]] * 10  # 错误的
lst = [[0, 1] for _ in range(10)] # 正确的
```

请注意 `list` 的 `*` 是, 浅拷贝!
按照错误实例创建出来的数组, 每一个元素指针都是**一样的**.
当其中一个元素被改变 (注意被替换不是被改变), 其余元素也会被同样的改变.

值得展开的是.
``` python
def func(lst=[]): ... # 错误

def func(lst=None): # 正确
    if not lst:
        lst = []
```

## 排序
对列表排序有两种办法.
``` python
lst.sort()
# 或
lst = sorted(lst)
```

第二种的好处是, 任意迭代器, 包括生成器都可排序.
但是, 第一种简洁, 而且可能会占用更小的空间 (未验证).

如果希望排序后是逆序, 加入 `reverse=True` 即可.
``` python
lst.sort(reverse=True)
lst = sorted(lst, reverse=True)
```

或者, 更高级的排序.
``` python
# 此例子的作用是按元组第一项排序
lst = [(1, 1), (1, 0), (2, 4)]
lst.sort(key=lambda x: x[1])
lst = sorted(lst, key=lambda x: x[1])
```

假如你希望用 `cmp` 函数而不是 `key` .
``` python
from functools import cmp_to_key

lst.sort(key=cmp_to_key(cmp))
```

## 前缀和
前缀和并不用手动求, 用 `accumulate()` 可以很简洁的求得.
``` python
from itertools import accumulate

prefix = list(accumulate(lst))
```

当然也可以应用在乘法上
``` python
from itertools import accumulate
from operator import mul

prefix = list(accumulate(lst, func=mul))
```

## for循环小技巧
当想要同时获取索引和元素时, 直接低级循环再取元素吗? 那太麻烦了吧!
``` python
# 0-base
for i, v in enumerate(lst):
    ...
# 1-base
for i, v in enumerate(lst, 1):
    ...
```
是不是方便多了?

当一个列表需要枚举所有 `l` 和 `r` 的时候.
``` python
from itertools import product

for l, r in product(a, repeat=2):
    ...
```

当遇到两层 `for` 的时候.
``` python
from itertools import product

for ai, bi in product(a, b):
    ...
```

又假如需要将两个长度相等的列表一起遍历呢?
``` python
for ai, bi in zip(a, b):
    ...
```

或者说, 同一个数组想枚举相邻元素. (Python 3.10+)
``` python
from itertools import pairwise

for i, j in pairwise(lst):
    ...
```

或者是, 需要分批处理. (Python 3.12+)
``` python
from itertools import batched

for lst_n in batched(lst, n):
    ...
```

# 搜索
## 二分
对于二分查找, Python 有 `bisect` 库.
``` python
idx = bisect.bisect(lst, x)
```

当然也可以使用 `key` 参数进行一些定制化的查找.

## 记忆化 dfs
比如计算 fib 时.
``` python
from functools import cache

@cache
def fib(n):
    if n <= 2:
        return 1
    return fib(n - 1) + fib(n - 2)
```

## 栈
Python 在 dfs 深度超过 $10^4$ 时会爆栈, 所以有以下模板.
``` python
def calc(n):
    if n <= 1:
        return n
    else:
        a = yield calc(n - 1)
        return a + 1


def event_loop(s):
    stk, last_rst = [s], None
    while stk:
        try:
            func, last_rst = stk[-1].send(last_rst), None
            stk.append(func)
        except StopIteration as e:
            last_rst = e.value
            stk.pop()
    return last_rst


print(f"Final result: {event_loop(calc(1000000))}")
```
使用 `yield` 返回"函数".

# 哈希
## 字典
defaultdict