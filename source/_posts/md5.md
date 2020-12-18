---
title: md5比较
comments: false
hide: false
date: 2020-12-9 13:55:00
urlname: md5
updated:
permalink:
tags:
categories:
  - 杂谈笔记
  - 学习笔记
---

MD5弱类型比较，强类型比较以及真实碰撞



<!-- more -->

#  MD5比较类型

## 弱类型比较

```php
if ($_POST['a'] != $_POST['b'] && md5($_POST['a']) == md5($_POST['b']))
```

弱类型比较因为php特性，0e开头会被识别为科学计数法，结果0=0比较成功

payload：

```php
a=QNKCDZO&b=aabg7XSs
```

只要是md5值为0e开头即可



---



## 强比较类型

```php
if ($_POST['a'] !== $_POST['b'] && md5($_POST['a']) === md5($_POST['b']))
```

强类型比较用数组绕过，md5()函数无法解出其数值，就会得到===强比较值相等

payload：

```php
a[]=111&b[]=aaa
```

传入数组即可



---



## 真实碰撞

```php
if ((string)$_POST['a'] !== (string)$_POST['b'] && md5($_POST['a']) === md5($_POST['b']))
```

真实md5碰撞，由于string()函数，不能输入数组只能输入字符串

payload：

```php
a=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2&b=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2
```

值不等，md5相等即可。md5碰撞。

---



##  0e开头MD5的MD5为0e的MD5

0e215962017 