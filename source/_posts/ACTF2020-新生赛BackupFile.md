---
title: ACTF2020 新生赛BackupFile 1
date: 2022-05-14 22:13:33
toc: false
tags:
- CTF
- 备份文件
categories: CTF
my: ACTF2020-BackupFile
---

这种题目看标题backupfile，之前在攻防世界做过类似的题目直接跟上/index.php.bak拿到文件

进行代码审计

```php
<?php
include_once "flag.php";

if(isset($_GET['key'])) {
    $key = $_GET['key'];
    if(!is_numeric($key)) {
        exit("Just num!");
    }
    $key = intval($key);
    $str = "123ffwsfwefwf24r2f32ir23jrw923rskfjwtsw54w3";
    if($key == $str) {
        echo $flag;
    }
}
else {
    echo "Try to find out source file!";
}
```

发现一个没有见过的intval()函数，万能百度

![](https://s2.loli.net/2022/05/15/DCIF8ayZduSen3M.png)

可以注意到is_numeric函数进行判断是否是数字，比较的时候使用的是==，弱类型比较，在比较的过程中因为$key是数字，所以str也会被隐性地转换成整型，即123,直接构造payload

```
/?key=123
```
