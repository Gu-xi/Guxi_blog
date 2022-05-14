---
title: NSSCTF Round#1 Basic
date: 2022-05-10 01:51:57
toc: false
tags: 
    - CTF
categories: CTF
my: NSSCTF-Round1
---

### **basic_check** 

第一次拿到web题的一血还是有点小激动的（借助了一下同队[暮渢](http://www.mufengnet.ltd)的思路hhh）

原题目来自[NSSCTF](https://www.ctfer.vip/)

首先我们拿到题目

![](https://s2.loli.net/2022/05/11/KzXjMAuH1xte4qF.png)

然后我们尝试按F12寻找思路，好的发现什么也没有，然后我就识图通过dirsearch查询是否有其他可以访问的网站地址

```python
python3 direarch.py -u http://1.14.71.254:28
```

![](https://s2.loli.net/2022/05/11/SBpoW9yJdFwNOeH.png)

两个都是主页 我们直接进入kali虚拟机  通过nikto扫一下

![](https://s2.loli.net/2022/05/11/zGf4tuhqJPogBxe.png)

漫长的等待  仔细看我们会发现

![](https://s2.loli.net/2022/05/11/QLxgi9qsUAVCoE5.png)

这个网站可以通过PUT方法  直接用firefox的扩展RESTClient

![](https://s2.loli.net/2022/05/11/RQBpWHYCm4vqP1e.png) 

上传成功，访问发现正常解析 直接蚁剑连接  在根目录下找到flag

![](https://s2.loli.net/2022/05/11/codtSCpQWwhLFg9.png)
