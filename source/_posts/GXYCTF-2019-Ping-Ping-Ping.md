---
title: GXYCTF 2019 Ping Ping Ping
date: 2022-05-30 14:21:02
toc: false
tags:
- 命令执行
categories: CTF-Web
my: Ping_Ping_ping
img: https://nssctf.wdf.ink/img/xmj/1.jpeg
---

打开题目开门见山直接告诉我们可以进行命令执行

首先这道题是ping我们先ping一下本地，好的执行的非常的慢，而Linux命令执行我们可以直接通过`;`进行后面的命令执行首先我们执行`ls`

```
?ip=;ls
```

![](https://nssctf.wdf.ink/img/xmj/image-20220530142548886.png)

太友好了这道题，我们只需要`cat flag` 好直接结束

![](https://nssctf.wdf.ink/img/xmj/image-20220530142640834.png)

过滤了空格，我们直接把空格改为`$IFS$1`，然后又出现了

![](https://nssctf.wdf.ink/img/xmj/image-20220530142740533.png)

这种直接拼接绕过拿到flag

payload:

```
?ip=;b=ag;cat$IFS$1fl$b.php
```

开始 以为自己做错了,一直没有回显，后面一直检查发现这个flag藏在源码里
