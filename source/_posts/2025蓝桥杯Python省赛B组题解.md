---
title: 2025蓝桥杯Python省赛B组题解
date: 2025-04-13 22:20:00
katex: true
tags:
  - 算法
  - 题解
  - 蓝桥杯
  - python
reprintPolicy: cc_by_nc_nd
---
# 试题A: 攻击次数
此题有歧义, 这里按照三个英雄都能攻击算.
使用`%`, 也就是取模 (求余) 运算符计算当前攻击力循环直至血量小于等于0打印回合并跳出循环即可.
## 最终答案
`103`
## 代码
``` python
hp = 2025
cnt = 0
while True:
    cnt += 1
    a = 5

    if cnt % 2 == 1:
        b = 15
    else:
        b = 2

    if cnt % 3 == 1:
        c = 2
    elif cnt % 3 == 2:
        c = 10
    else:
        c = 7

    hp -= a + b + c
    if hp <= 0:
        print(cnt)
        break
    
```

---
# 试题B: 最长字符串
暴力匹配可能还是有点太暴力了, 一种比较合理的做法是创建两个集合 (`set`), 一个用于存放排序后的美丽串 (假定为 $S_1$ ), 另一个存放未排序的美丽串 (假定为 $S_2$ ), 以长度为键排序输入的数据, 而后依次取出排序后的数据进行如下操作:
- 若满足以下两个条件中的一个即为美丽串, 将其加入 $S_2$ 中, 排序后加入 $S_1$ 中
	- 长度为1.
	- 将 $c_1, c_2 ... c_{n-1}$ 按字典序排序, 排序后的字符串是否在 $S_1$ 中.

而后找出$s_2$最长的字符串, 排序取出第一个即可.

## 最终答案
`afplcu`
## 代码
_待补全_

---
# 试题C: LQ图形
没什么好说的, 就是for循环 $h$ 次, 每次打印 $w$ 个`Q`,再for循环 $w$ 次每次打印 $w+v$ 个`Q`.
## 代码
```python
w, h, v = map(int, input().split())
for _ in range(h):
    print("Q" * w)

for _ in range(w):
    print("Q" * (w + v))
```

---
# 试题D: 最多次数
_待补全_

---
# 试题E: A·B Problem
# 80分做法
暴力尝试可能的两数乘积, 接着暴力尝试所有可能两数之和.

### 代码
``` python
from collections import defaultdict


n = int(input())
cnt_d = defaultdict(int)
lst = set()
for i in range(1, n + 1):
    for j in range(1, n + 1):
        if i * j <= n:
            cnt_d[i * j] += 1
            lst.add(i * j)
        else:
            break
            
cnt = 0
for i in lst:
    for j in lst:
        if j + i <= n:
            cnt += cnt_d[j] * cnt_d[i]

print(cnt)
```

## 满分做法
筛法
_待补全_

---
# 试题F: 园艺
暴力尝试所以间隔 $d$ 并尝试间隔 $d$ 时所有可能抽取的树木. 可以证明时间复杂度不超过 $O(n^2)$.

# 代码实现
``` python
n = int(input())
a = list(map(int, input().split()))


max_l = 0
for d in range(1, n + 1): # 尝试所有d
    if n // d + 1 < max_l: #如果当前d已不足以更新max_l则退出循环
        break
    for dif in range(d): # 偏移
        last = 0
        cnt = 0
        for j in range(dif, n, d): #记录最长升序序列
            if last < a[j]:
                cnt += 1
            else:
                cnt = 1
            last = a[j]
            if cnt > max_l:
                max_l = cnt
print(max_l)

```

---
# 试题G: 书架还原
算是一道小思维题. 我们可以将书籍的排列看作一个置换, 通过分析置换环的性质解决此题. 具体上是: 只要把每本书移动到该有的位置, 而被替换出来的书再继续移动到它应该在的位置, 这样迭代下去必然能以最小代价还原排列 (每本书最多一次移动). 如果不需要实际操作而只是计算最小移动次数, 可以通过统计置换环的数量来实现. 因为在这个置换中, 每个环内的元素会形成循环依赖, 对于一个包含 $k$ 个元素的环,只需要 $k-1$ 次交换即可将所有元素归位. 可以证明时间复杂度为 $O(n)$ . 

## 代码
这里给出两种做法:
### 解法1: 直接计数
``` python
def clac(a: list, s: int, vis: set):
    sc = s
    cnt = 0
    while True: # 判环
        vis.add(sc)
        sc = a[sc - 1]
        if s == sc:
            break
        cnt += 1
    return cnt


n = int(input())
a = list(map(int, input().split()))
vis = set()
cnt = 0
for i in range(1, n): # 把每个环的计数相加
    if i not in vis:
        cnt += clac(a, i, vis)

print(cnt)
```

### 解法2: 记环数
``` python
def clac(a: list, s: int, vis: set):
    sc = s
    while True:
        vis.add(sc)
        sc = a[sc - 1]
        if s == sc:
            break


n = int(input())
a = list(map(int, input().split()))
vis = set()
cnt = n
for i in range(1, n+1):
    if i not in vis:
        cnt -= 1
        clac(a, i, vis)

print(cnt)
```
---
# 试题H: 异或和
## 40分做法
按题面意思计算即可
### 代码
``` python
n = int(input())
a = list(map(int, input().split()))
print(sum(sum((a[i] ^ a[j]) * (j - i) for j in range(i + 1, n)) for i in range(n - 1)))
```