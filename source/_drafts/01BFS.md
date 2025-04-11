---
title: 01BFS
date: 2025-04-11 18:15:00
katex: true
tags:
  - 算法
reprintPolicy: cc_by_nc_nd
---
# 前言
01BFS个人认为是Dijkstra最短路算法的前置知识, 01BFS可以由BFS直接演变过来, 比较好理解, 而理解后对学习Dijkstra算法也有一定帮助.

# 算法
## 简介
一般的BFS计算路径最近节点距离时权重都为"1", 而01BFS加入了权重为0的节点. 为0说明对长度计算不会造成影响, 所以我们可以很简单的把权重为0的节点放在BFS的队首. 既然要同时对两边进行插入, 那不难想到应该用*双端队列*.
## 实现
### BFS
我们从一个简单的BFS开始讲起.
加入我们有个图$e$, 权值都为1, 想要找到最短路广搜是最简单的.
``` python
from typing import Any
def bfs(e: dict[Any, list[Any]], s: Any):
	vis = set()
    queue = deque([(0, s)])
    dis = defaultdict(lambda: float("+inf"))
    dis[s] = 0
    while queue:
        _, u = queue.popleft()
        if u in vis:
            continue
        vis.add(u)
        for v in e[u]:
            dis[v] = min(dis[u] + 1, dis[v])
            queue.append((dis[v], v))
    return dis
```
