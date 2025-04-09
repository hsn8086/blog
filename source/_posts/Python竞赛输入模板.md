---
title: Python竞赛输入模板
date: 2025-03-26 14:51:41
katex: true
tags:
  - 算法
  - python
  - 模板
reprintPolicy: cc_by_nc_nd
---
# 速度优先
``` python
input=sys.stdin.readline
n, m = map(int, input().split())
a = list(map(int, input().split()))
```
# 内存优先 / 防止错误空行
``` python
from collections.abc import Generator
import sys


def chunk_reader(func=int, chunk_size=1 << 15) -> Generator[int, None, None]:
    inp_cache = ""
    while True:
        chunk = sys.stdin.read(chunk_size) # 读入一块
        if not chunk: # 判断是否为结尾
            if inp_cache: # 判断缓存内是否还有内容
                yield func(inp_cache) # 输出
                del inp_cache # 删除缓存
            break # 跳出循环

        for c in chunk: 
            if c.isspace(): # 判断当前字符是否是空格或者换行
                if inp_cache: # 如果当前缓存有内容, 即代表此内容已经到了结尾
                    yield func(inp_cache) # 输出
                    del inp_cache 
                    inp_cache = "" # 删除缓存并重新初始化
            else:
                inp_cache += c # 否则把字符串加入缓存
            del c # 删除字符
        del chunk # 删除区块


inp = chunk_reader(chunk_size=1 << 18)
n, m = next(inp), next(inp)
a = [next(inp) for _ in range(n)]
```