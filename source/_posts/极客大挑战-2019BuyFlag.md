---
title: 极客大挑战 2019 BuyFlag
date: 2022-05-28 19:43:02
toc: false
tags:
- CTF
- 数组绕过
categories: CTF
my: Buyflag
---

打开题目看到右上角，点击payflag，这道题，直接告诉我们需要什么条件，按F12 看见一个很明显的提示

```html
<!--
	~~~post money and password~~~
if (isset($_POST['password'])) {
	$password = $_POST['password'];
	if (is_numeric($password)) {
		echo "password can't be number</br>";
	}elseif ($password == 404) {
		echo "Password Right!</br>";
	}
}
-->
```

告诉我们我们的`password`需要是404但是我们要绕过`is_numeric`，看见弱等于，我们直接另`paaword=404a`成功绕过，再将money的值为100000000发现

![](https://nssctf.wdf.ink/img/xmj/image-20220528195846219.png)

这里有一个验证，提示我们只有cuit's students可以做这件事，这里一般验证都是在cookie，直接看向我们的cookie果然有一个

![](https://nssctf.wdf.ink/img/xmj/image-20220528195930419.png)

将值改为1，刷新网页，又告诉我们我们的长度太长

![](https://nssctf.wdf.ink/img/xmj/image-20220528200013849.png)

这里就有两个方式可以绕过

#### 法1:

将money改为数组

![](https://nssctf.wdf.ink/img/xmj/image-20220528200056841.png)

#### 法2：

用科学记数法

![](https://nssctf.wdf.ink/img/xmj/image-20220528200131344.png)

直接得到flag
