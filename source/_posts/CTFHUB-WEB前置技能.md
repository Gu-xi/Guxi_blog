---
title: CTFHUB-WEB前置技能
date: 2022-05-14 22:12:16
tags:
    - CTF
categories: CTF
my: CTFHUB_prerequisite-skills
---

## ***请求方式***

![](https://s2.loli.net/2022/05/15/5habHieW27Tly1g.png)

翻译一下：

HTTP 方法是 GET

使用CTFHUB方法，我给你flag。

提示：如果你遇到「HTTP Method Not Allowed」错误，你应该请求 index.php。

非常明显，直接burp抓包该请求方式为CTFHUB就拿到flag

## ***302跳转***

![](https://s2.loli.net/2022/05/15/3gurKsYONPFqpwk.png)

疯狂点击“Give me Flag” 没有任何的反应，看到题目302跳转（302重定向又称之为暂时性转移）直接burp抓包一个包一个包的放到repeater里来看，就能发现flag

## ***Cookie***

网页显示：我是访客只有admin能够拿到flag，题目cookie直接F12找到cookie，把admin的值改成1拿到flag

## ***基本认证***

打开发现有个“click”点一下发现弹出验证框，题目有个附件给了弱口令，直接burp抓包爆破。但是看见请求头里什么参数没有显示仔细看看发现

![](https://s2.loli.net/2022/05/15/iNV5HvaJ7QebWKZ.png)

这里有个授权书，后面看不懂，感觉像是什么加密后的，有大写，感觉没有等号，不像base64（笑死结果就是），到处试，最后base64 解密出来是

![](https://s2.loli.net/2022/05/15/CdR58zqhOEKaZVt.png)

就是我自己输入的账号和密码，用burp的爆破功能

![](https://s2.loli.net/2022/05/15/lHoFtfAdNhUD785.png)

![](https://s2.loli.net/2022/05/15/2jkvSONiUHDsYab.png)**这里直接上传题目给的附件**

![](https://s2.loli.net/2022/05/15/Z8CwijMhoeEDYgP.png)

这里我试图直接爆破，但是什么结果都没有仔细直接发包试了一下

![](https://s2.loli.net/2022/05/15/IvfVGNYL3K67qWU.png)账户很有可能就是admin

![](https://s2.loli.net/2022/05/15/DiWBF3sfQPGT5SC.png)添加前缀:"admin:"   用户名设置为admin

![](https://s2.loli.net/2022/05/15/Zy7A6UfeYSWvPaJ.png)base64编码

开始爆破就可以拿到flag

## ***响应包源码***

F12拿到flag
