---
title: Python防爆栈模板
date: 2025-04-23 18:51:41
katex: true
tags:
  - 算法
  - python
  - 模板
reprintPolicy: cc_by_nc_nd
---
# 简介
类似协程, 可以`yield`结果, 记得`return`.

# 代码
``` python
from typing import Generator, Any



def calc(n):
    if n <= 1:
        return n
    else:
        a = yield calc(n - 1)
        return a + 1


def event_loop(start_gen: Generator):
    stack: list[Generator] = [start_gen]

    last_result: Any = None

    while stack:
        now_task = stack[-1]
        try:
            next_val = now_task.send(last_result)
            last_result = None
            if isinstance(next_val, Generator):
                stack.append(next_val)
            else:
                last_result = next_val
                stack.pop()

        except StopIteration as e:
            stack.pop()

            last_result = e.value

        except TypeError as e:
            if (
                last_result is None
                and "can't send non-None value to a just-started generator"
                not in str(e)
            ):
                last_result = None
                continue
            else:
                raise e

    return last_result


print(f"Final result: {event_loop(calc(1000000))}")

```