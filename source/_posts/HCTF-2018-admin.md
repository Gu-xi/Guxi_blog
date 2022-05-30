---
title: HCTF 2018 admin
date: 2022-05-28 20:03:15
toc: false
tags:
- Session伪造
categories: CTF-Web
my: HCTF_2018_admin
---

进入题目首先F12查看源码，告诉我们我们不是admin，首先看cookie有没有办法修改，发现什么都没有，点击右上角的login，尝试admin的密码123 直接拿到flag，~~好本题结束~~。

自己注册一个，发现没有特别的，右上角多了

<img src="https://nssctf.wdf.ink/img/xmj/image-20220528201006578.png" style="zoom:50%;" />

检查一下每一个的源码，在`change password`上看见了一个github的网站

![](https://nssctf.wdf.ink/img/xmj/image-20220528201128739.png)

尝试访问，发现是这道题的源码

#### 法1:flask session伪造

​         flask中session是存储在客户端cookie中的，也就是存储在本地。flask仅仅对数据进行了签名。签名的作用是防篡改，而无法防止被读取。而flask并没有提供加密操作，所以其session的全部内容都是可以在客户端读取的，这就可能造成一些安全问题（百度的）

```python
import sys
import zlib
from base64 import b64decode
from flask.sessions import session_json_serializer
from itsdangerous import base64_decode
def decryption(payload):
    payload, sig = payload.rsplit(b'.', 1)
    payload, timestamp = payload.rsplit(b'.', 1)
    decompress = False
    if payload.startswith(b'.'):
        payload = payload[1:]
        decompress = True
    try:
        payload = base64_decode(payload)
    except Exception as e:
        raise Exception('Could not base64 decode the payload because of '
                         'an exception')
    if decompress:
        try:
            payload = zlib.decompress(payload)
        except Exception as e:
            raise Exception('Could not zlib decompress the payload before '
                             'decoding the payload')
    return session_json_serializer.loads(payload)
if __name__ == '__main__':
    print(decryption(sys.argv[1].encode()))

```

这里我们需要一个SECRET_KEY，在给我们的源码里找发现config.py里

![](https://nssctf.wdf.ink/img/xmj/image-20220528210917372.png)

直接给了我们secret_key，接下来我们直接伪造一个name为admin的session

参考[文章](https://www.leavesongs.com/PENETRATION/client-session-security.html) 脚本[链接](https://github.com/noraj/flask-session-cookie-manager)

得到后提交得到flag

#### 法2: Unicode欺骗

在看源码的时候发现routes.py文件里修改密码时会修改为小写

![](https://nssctf.wdf.ink/img/xmj/image-20220528212213502.png)

观察最后

![](https://nssctf.wdf.ink/img/xmj/image-20220528212304827.png)

我们注册ᴬᴰᴹᴵᴺ用户，然后在用ᴬᴰᴹᴵᴺ用户登录，因为在login函数里使用了一次nodeprep.prepare函数，因此我们登录上去看到的用户名为ADMIN，此时我们再修改密码，又调用了一次nodeprep.prepare函数将name转换为admin，然后我们就可以改掉admin的密码，最后利用admin账号登录即可拿到flag。

转换时过程如下ᴬᴰᴹᴵᴺ -> ADMIN -> admin

