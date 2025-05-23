---
title: Python 树状数组
date: 2025-05-21 12:43:00
katex: true
tags:
  - 算法
  - python
  - 数据结构
  - 模板
reprintPolicy: cc_by_nc_nd
---
# 一般树状数组
单点更改, 区间查询
``` python
class FenwickTree:
    def __init__(self, size):
        self.size = size
        self.tree = [0] * (self.size + 1)

    def update(self, index, delta):
        while index <= self.size:
            self.tree[index] += delta
            index += index & -index

    def query_prefix(self, index):
        res = 0
        while index > 0:
            res += self.tree[index]
            index -= index & -index
        return res

    def query(self, index):
        return self.range_query(index, index)

    def range_query(self, left, right):
        return self.query_prefix(right) - self.query_prefix(left - 1)
```
## 例题
[P3374 【模板】树状数组 1](https://www.luogu.com.cn/problem/P3374)
# 差分树状数组
区间更改, 单点查询
``` python
class DiffFenwickTree:
    def __init__(self, size):
        self.size = size
        self.tree = [0] * (self.size + 1)

    def _update(self, index, delta):
        while index <= self.size:
            self.tree[index] += delta
            index += index & -index

    def update(self, index, delta):
        self._update(index, delta)
        self._update(index + 1, -delta)

    def update_range(self, start, end, delta):
        self._update(start, delta)
        self._update(end + 1, -delta)

    def query(self, index):
        res = 0
        while index > 0:
            res += self.tree[index]
            index -= index & -index
        return res

```
## 例题
[P3368 【模板】树状数组 2](https://www.luogu.com.cn/problem/P3368)

# 双树状数组
水群知道的, 比较有趣的知识, 证明以后再说.

支持区间修改区间查询

``` python
class DoubleFenwickTree:
    def __init__(self, size):
        self.ft1 = FenwickTree(size)
        self.ft2 = FenwickTree(size)

    def _update(self, index, delta):
        self.ft1.update(index, delta)
        self.ft2.update(index, delta * index)

    def update(self, index, delta):
        self.range_update(index, index, delta)

    def range_update(self, left, right, delta):
        self._update(left, delta)
        self._update(right + 1, -delta)

    def query_prefix(self, index):
        return (index + 1) * self.ft1.query_prefix(index) - self.ft2.query_prefix(index)

    def range_query(self, left, right):
        return self.query_prefix(right) - self.query_prefix(left - 1)

    def query(self, index):
        return self.range_query(index, index)
```