---
title: HCTF 2018 Warmup
date: 2022-05-14 22:08:52
toc: false
tags:
- 代码审计
categories: CTF-Web
my: HCTF2018_Warmup
---

进入题目只有一个滑稽  我们直接F12看源码发现有一个非常明显的提示 我们直接访问source.php

![](https://s2.loli.net/2022/05/14/UJPSW2dD8os1lG5.png)

得到源码

```php
<?php
    highlight_file(__FILE__);
    class emmm
    {
        public static function checkFile(&$page)
        {
            $whitelist = ["source"=>"source.php","hint"=>"hint.php"];
            if (! isset($page) || !is_string($page)) {
                echo "you can't see it";
                return false;
            }

            if (in_array($page, $whitelist)) {
                return true;
            }

            $_page = mb_substr(
                $page,
                0,
                mb_strpos($page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }

            $_page = urldecode($page);
            $_page = mb_substr(
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }
            echo "you can't see it";
            return false;
        }
    }

    if (! empty($_REQUEST['file'])
        && is_string($_REQUEST['file'])
        && emmm::checkFile($_REQUEST['file'])
    ) {
        include $_REQUEST['file'];
        exit;
    } else {
        echo "<br><img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
    }  
?>
```

新的提示！hint.php直接访问 （这也太直白了hhh）

![image.png](https://s2.loli.net/2022/05/14/SIdFDHzUeRYuQpa.png)

我们继续代码审计，下面这段代码的意思是source.php接收一个file参数，当file这个参数满足以下三个条件时返回file指定的文件，否则输出一张图片。

```php
if (! empty($_REQUEST['file'])
        && is_string($_REQUEST['file'])
        && emmm::checkFile($_REQUEST['file'])
    ) {
        include $_REQUEST['file'];
        exit;
    } else {
        echo "<br><img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
    }  
```

1. file参数不为空
2. file参数是一个string类型
3. file参数能够通过checkFile的检验

```php
$whitelist = ["source"=>"source.php","hint"=>"hint.php"];//设定白名单
if (! isset($page) || !is_string($page)) {
                echo "you can't see it";
                return false;
            }
//如果file不存在或非字符串，输出"you can't see it"并返回false。


if (in_array($page, $whitelist)) {
                return true;
            }
//如果file参数在白名单中，返回true。访问 source.php?file=source.php 可以看到输出了两次页面源码，证明在这里返回了true。


$_page = mb_substr(
                $page,
                0,
                mb_strpos($page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }
            $_page = urldecode($page);//上下部分完全相同
            $_page = mb_substr(
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }
```

这段代码在 *$_page = urldecode($page);* 上下的部分都是完全一样的，只是为了防止用户传参时符号被urlencode而无法解析。它的作用是在用户传的file中，从开始截取到第一个问号处（不含问号），如果file中不存在问号，就在file后加一个。将截取后的字符串再与白名单做对比，如果可以就返回true。

我们可以可以通过访问 *source.php?file=source.php?* 来进行验证，这时可以看到页面源码只输出了一次，但并没有输出"you can't see it"，证明白名单检验通过，但source.php?这个文件是不存在的，所以没有任何输出

那么我们就可以利用这里来访问到根目录下的ffffllllaaaagggg

直接构造payload:

```
sosource.php?file=source.php?/../../../../ffffllllaaaagggg
```

在source.php?后加一个"/"这时source.php会被视为一个文件夹，后面每一级的".."意为上一层文件夹，通过不断尝试加入"../"最后可以知道具体的目录层级，以访问到ffffllllaaaagggg。

![image.png](https://s2.loli.net/2022/05/14/DrqKcUvBheYSZIk.png)
