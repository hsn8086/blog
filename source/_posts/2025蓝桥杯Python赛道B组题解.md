# 试题A:攻击次数
此题有歧义, 这里按照三个英雄都能攻击算.
使用`%`, 也就是取模(求余)运算符计算当前攻击力循环直至血量小于等于0打印回合并跳出循环即可.
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
    if hp<= 0:
        print(cnt)
        break
    
```