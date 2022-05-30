---
title: Easy_tornado
date: 2022-05-27 20:55:57
toc: false
tags:
- SSTI
categories: CTF-Web
my: easy_tornado
---

拿到题目看见三个网址，分别去访问并观察URL变化

#### flag.txt（直接告诉flag的目录名）

![](https://nssctf.wdf.ink/img/xmj/image-20220527210238384.png)

### welcome.txt

渲染？

![](https://nssctf.wdf.ink/img/xmj/image-20220527210307999.png)

### hints.txt

![](https://nssctf.wdf.ink/img/xmj/image-20220527210324883.png)

仔细观察URL都是

```
file?filename=***&filehash=***
```

尝试修改文件名为/fllllllllllllag发现报错，并且URL传参数变成了新的

![](https://nssctf.wdf.ink/img/xmj/image-20220527210912959.png)

最重要的一看就是hints.txt告诉了我们后面的`filehash`的值是将cookie_secret和md5加密后的filename再次md5加密，那么现在我们已经知道了flag的名字为/fllllllllllllag

现在的主要目标就是找到cookie_secret

想起来题目tornado，查询tornado框架是python的web框架，这个框架可以存在读取配置文件的漏洞，而render就是SSTI服务器模版漏洞。

尝试修改下方的Error，发现回显跟随改变，一瞬间就想到了SSTI，构造如下payload

```
error?msg={{handler.settings}}
```

直接拿到cookie_secret

![](https://nssctf.wdf.ink/img/xmj/image-20220528001145364.png)

将/fllllllllllllag进行md5加密（3bf9f6cf685a6dd8defadabfb41a03a1）后拼接到cookie_secret后面，再一次md5加密

即：7d575eba-6f27-446a-aabc-9965814375253bf9f6cf685a6dd8defadabfb41a03a1

进行md5加密

![](https://nssctf.wdf.ink/img/xmj/image-20220528001438040.png)

再进行传参拿到flag

![](https://nssctf.wdf.ink/img/xmj/image-20220528001602525.png)
