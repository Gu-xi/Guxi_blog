---
title: XDCTF 2015Filemanager
date: 2022-05-14 22:11:24
toc: false
tags:
    - CTF
categories: CTF
my: XDCTF_2015_Filemanager
---

拿到题目  以为是文件上传简单题目 后面多次碰壁发现一点也不简单！

![](https://s2.loli.net/2022/05/14/fEat6CbZweg8Oyh.png)

我们看见rename部分  想通过rename更改文件后缀  但这里显示只包含文件名称

![](https://s2.loli.net/2022/05/14/uHxzE7cW4VXbrKN.png)

试图寻找源码  通过dirserarch扫描  我们找到了一个/www.tar.gz  访问下载网站源码

![](https://s2.loli.net/2022/05/14/bV3WcYn2tNLIEOe.png)

仔细进行代码审计  首先由白名单限制  我们只能上传如下后缀文件

![](https://s2.loli.net/2022/05/14/H6X5JgKNx4mO8qc.png)

下面这段代码 发现其将被修改的旧文件名的扩展名保留到新文件名的扩展名上

![](https://s2.loli.net/2022/05/14/B8VfcYEb4H5WFrL.png)

通过同队web佬[C1c](https://www.c1c.ink/)的讲解想到了SQL注入使其文件扩展为空然后进行更改文件名 

我们看见下面的update语句

```php
$re = $db->query("update `file` set `filename`='{$req['newname']}', `oldname`='{$result['filename']}' where `fid`={$result['fid']}");
```

想去闭合文件名    使得下面的$result["extension"]为空直接进行如下构造

名字构造为',extension='.jpg     使其进行闭合

![](https://s2.loli.net/2022/05/14/1GYdaDoJZybvQNu.png)

后台得到如下闭合

![](https://s2.loli.net/2022/05/14/cS4zZwexjEfrROy.png)

然后修改名字   这一步利用updata语句直接将1.jpg.___后缀为空,只有1.jpg的文件名被传入数据库）

此时该文件的extension为空

![](https://s2.loli.net/2022/05/14/BuaxsdRC1PWpAEh.png)



![](https://s2.loli.net/2022/05/14/VdE5BFM4hLxyOXc.png)

然后我们再上传带有木马的1.jpg文件  因为数据库里的为1.jpg.jpg 故没有相同的文件可以上传

![](https://s2.loli.net/2022/05/14/vMqDcdxHNBa7Elk.png)

因为构造了1.jpg的extension为空，所以现在是修改的文件名  可以加上.jpg  相当于修改了后缀名

![](https://s2.loli.net/2022/05/14/IPXl5gRZwC6SOQB.png)

此时就修改成功了

![](https://s2.loli.net/2022/05/14/Eqd9gVIjz4HfpsD.png)

直接蚁剑连接查看根目录  找到flag

![](https://s2.loli.net/2022/05/14/GDQ7UjInrPekgJw.png)

此题目就到此结束   我们也可以去查看upload文件下  发现是修改的第二次我们上传的带有木马的1.jpg

![](https://s2.loli.net/2022/05/14/yWXiVIGUQx6ackd.png)

