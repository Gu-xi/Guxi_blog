---
title: CTFHUB-信息泄漏
date: 2022-05-14 22:12:33
tags:
- 备份文件
- git 泄漏
- SVN 泄漏
- HG 泄漏
categories: CTF-Web
my: CTFHUB_information-leakage
---

## ***目录遍历***

看到题目我们一个一个找就找到答案

## ***PHPINFO***

直接command+F（Windows是control+F）搜索flag直接拿到

## ***备份文件下载***

### ***网站源码***

直接扫后台，下载源码，打开flag发现没有，直接去访问拿到flag

### ***bak文件***

看到题目我们直接访问index.php.bak下载打开拿到flag

### ***vim缓存***

没做过这种题，访问index.php看源码什么都没有，好的直接扫一下后台，看看有没有什么地方可以访问拿到源码，很好什么都没扫出来。我选择百度了解到vim在编辑文档的过程中如果异常退出，会产生缓存文件，第一次产生的缓存文件后缀为.swp，后面会产生swo等。尝试访问index.php.swp和index.php.swo发现都是404直接疑惑，后面才知道要访问.index.php.swp(index前面要加个'.')下载下来发现是二进制文件，打开全是乱码，试图vim打开编辑，直接崩掉自己的终端笑死。后面才知道要用如下指令打开拿到flag.

![](https://s2.loli.net/2022/05/15/pxTg2zJCqIEsY7o.png)

### ***.DS_Store***

看到题目描述macos，在自己电脑上找了一下，没找到哈哈哈。

没有思路 开扫目录（其他的题目别乱扫）蚌埠住了什么也没有

没有思路 直接百度发现别人的dirsearch就能扫出来麻掉了，打开发现打不开，用指令cat 抓取发现flag is here去网站访问这个txt文件（一开始全部输入发现404）拿到flag

## ***Git泄漏***

### ***Log***

题目描述说了GitHack给了思路，用指令python GitHack.py <url>然后cd到dist文件夹里的对应网站文件夹 git log看到过去

![](https://s2.loli.net/2022/05/15/ZMON9baQwYKBkqx.png)发现中间add flag

用git reset --hard 37ebed6(可不输完整)将文件夹里的文件到过去的版本中

![](https://s2.loli.net/2022/05/15/G5FWAc7sI4dBuVK.png)

打开文件夹发现txt文件打开就拿到了flag

### ***Stash***

用GitHack工具扫描后

#### 法1

```
cat .git/refs/stash  //来得到stash对应的hash值
用git diff <hash>    //拿到flag
```

#### 法2

```
git stash list    //拿到list
git stash pop    //从 git 栈中弹出来一个文件
cat <对应txt文件>  //拿到flag
```

#### 法3

```
git stash         
git stash apply  //恢复，即可获得 Flag 文件
cat <对应txt文件>  //拿到flag
```

### ***Index***

#### 法1

同上方log即可拿到flag

#### 法2

用git checkout 看到文件名在用git checkout <文件名>拿到flag

命令解释

创建新分支：git branch branchName

切换到新分支：git checkout branchName

## ***SVN泄漏***

SVN泄露又一次到了我的知识盲区  [工具](https://github.com/kost/dvcs-ripper)里的工具，看了一下下面的使用教程，直接麻掉，找到我们这道题SVN泄漏

![](https://s2.loli.net/2022/05/15/rT1MIwnfABZKcbV.png)

用指令直接来！mac用如下指令

```
./rip-svn.pl  <url>
```

![](https://s2.loli.net/2022/05/15/Jew2F3Kjbk4zX1D.png)

用ls -a看见隐藏文件

![](https://s2.loli.net/2022/05/15/9cOhPrLb1EelV5M.png)

cd .svn 到文件  ls 

![](https://s2.loli.net/2022/05/15/WmlpoC5NMy4VGXd.png)

cd pristine   然后ls 

![](https://s2.loli.net/2022/05/15/g5PMtjrYhxReNBA.png)

cd bf     然后ls 再用cat指令拿到文件内容发现是网站的原来的源码

重新 cd d0  ls  然后cat文件  拿到flag

也可以用tree指令查看

## ***HG泄漏***

[工具](https://github.com/kost/dvcs-ripper)

tree .hg看到如下

![](https://s2.loli.net/2022/05/15/txGhNyQU1Jfmpd5.png)

cd .hg/store   然后cat fncache  拿到如下 直接访问/flag_401618203.txt即可拿到flag

![](https://s2.loli.net/2022/05/15/IPizkNm34apchb8.png)
