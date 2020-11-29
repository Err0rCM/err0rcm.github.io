---
title: 网络空间安全期末Write-up
urlname: examination-Write-up
date: 2020-10-20 22:27:25
updated: 
comments: false
tags: 
  - Misc
categories: 
  - Write-Up
permalink: 
---

<h3>题解</h3>
<p>by:gyy</p>
<h2>前言</h2>
<p>这学期选修课有个网络空间安全课，算创新创业学分。看了下题目，都是Misc题，很简单，简单分享下解法</p>
<h2>pwd</h2>
<h3>考点：Web网页</h3>
<p>题目链接： <a class="url" href="http://192.168.19.81:5000/24100020-c728595d/" target="_blank" rel="noopener noreferrer">http://192.168.19.81:5000/24100020-c728595d/</a> （内网）</p>
<p> </p>
<p>进去提示密码真的为空（真的？</p>
<p><img class="size-full wp-image-62 aligncenter" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020200656926.png" alt="" width="580" height="689" /></p>
<p>查看源码发现传参a，联想空密码，输入NULL，得到flag</p>
<p><img class="aligncenter wp-image-61 size-full" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020205708555.png" alt="" width="1920" height="1040" /></p>
<h5>问题解决： flag{547a0e62-2c73-4e8c-a936-c6d6f3e4da05}</h5>
<p>payload:</p>
<pre><code class="language-python" lang="python"># -*- coding: UTF-8 -*-
import requests

url ="http://192.168.19.81:5000/24100020-c728595d/psw.php"

data ={
    "a" : "NULL"
}

#a=

a = requests.post(url,data)

print(a.text)
</code></pre>

<hr />
<h2>CTF</h2>
<h3>考点：基础知识</h3>
<p>题目链接： <a class="url" href="http://192.168.19.81:5000/2310027-462db4f8/" target="_blank" rel="noopener noreferrer">http://192.168.19.81:5000/2310027-462db4f8/</a></p>
<p>题目问CTF发源地是哪，老老实实直接回答，答案 DEFCON ，不会就<a href="http://www.baidu.com">点这</a></p>
<p><img class="alignnone size-full wp-image-63" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020205116307.png" alt="" width="1920" height="1040" /></p>
<h5>问题解决：flag{eca12959-53ea-4fa9-85d0-b5425cd930c5}</h5>
<p>payload:</p>
<pre><code class="language-python" lang="python"># -*- coding: UTF-8 -*-
import requests

url ="http://192.168.19.81:5000/2310027-462db4f8/me.php"

data ={
    "solution" : "DEFCON" ,
    "submit" : "提交"
}

#solution=DEFCON&submit=

a = requests.post(url,data)

print(a.text)
</code></pre>
<p> </p>
<hr />
<h2>隐藏的编码</h2>
<h3>考点：基础密码学</h3>
<p>题目链接：<a href="http://192.168.19.81:5000/24100020-c728595d">http://192.168.19.81:5000/24100020-c728595d/</a></p>
<p>火眼金睛找出密文中密码</p>
<pre><code>adslkjadsl a ,zmxnc,zmc,zxcopaweqwk;l;l;l;l;l;l;l;l;l;l;l;ldkm,ZGY1czRh==.zxmczxkchzxkhiqewupipsad;l;l;l;l;l;l;l;l;l;l;l;l
</code></pre>
<p>这题出的不好，第一眼看到base64编码，其他一开始不清楚，解码一下提交，就没了。这题考点出的不太好。</p>
<p>总之就是了解一下<a href="http://err0r.top:4100/misc/encode/computer-zh/#base" target="_blank" rel="noopener noreferrer">base64编码</a>， 可含有字母 A-Z、a-z、数字 0-9 + /和=</p>
<p> </p>
<p><img class="alignnone size-full wp-image-64" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020210254413.png" alt="" width="1920" height="1040" /></p>
<h5>问题解决：flag{80b3f46d-f4c8-424c-bc8e-a795bf5d3612}</h5>
<p>payload：</p>
<pre><code class="language-python" lang="python"># -*- coding: UTF-8 -*-
import requests
import base64

encode = "ZGY1czRh=="

url ="http://192.168.19.81:5000/24100002-2bb77f6e/"

s = base64.b64decode(encode)

data ={
    "password" : s
}

#password=

a = requests.post(url,data)

print(a.text)
</code></pre>
<hr />
<h2>金絮其外,败絮其中</h2>
<h3>考点：文件bin</h3>
<p>题目链接： <a class="url" href="http://192.168.19.81:5000/100510010-e851d694/" target="_blank" rel="noopener noreferrer">http://192.168.19.81:5000/100510010-e851d694/</a></p>
<p>题目提示看起来和css有关，f12看源码发现css果然有东西，三个css分别是<code>login.css</code>，<code>username.css</code>，<code>password.css</code>。</p>
<p>习惯性先看<code>password.css</code>，发现异常，不是正常的css样式，colour属性不对，于是把一串复制下来</p>
<pre><code>#colour =666c61677b61323334303262642d323065302d343233362d616465362d3164646635623866356132397d
</code></pre>
<p>字符串只出现了数字0-9和小写字母，盲猜base16，脚本跑一下，成功，解得flag。</p>
<h5>问题解决：flag{a23402bd-20e0-4236-ade6-1ddf5b8f5a29}</h5>
<p>payload：</p>
<pre><code class="language-python" lang="python"># -*- coding: UTF-8 -*-
import requests
import base64

a = "666c61677b61323334303262642d323065302d343233362d616465362d3164646635623866356132397d"

b = a.swapcase()

#print(b)

decode = base64.b16decode(b)

print(decode)
</code></pre>
<hr />
<h2>tupian</h2>
<h3>考点：文件bin 文件格式</h3>
<p>题目链接： <a class="url" href="http://192.168.19.81:5000/2210018-d7ae168f/gif.gif" target="_blank" rel="noopener noreferrer">http://192.168.19.81:5000/2210018-d7ae168f/gif.gif</a></p>
<p>题目已同步上传至<a href="http://err0r.top:8000/challenges#tupian" target="_blank" rel="noopener noreferrer">本站CTFd</a></p>
<p>进去拿到个gif，至少它说是的...</p>
<p>无法加载，应该是改东西了</p>
<p>听说老曹最后直接给flag了，确实，因为没有工具根本做不出来的</p>
<p> </p>
<p>进入Linux虚拟机把文件下载下来，Linux运行</p>
<p><code>wget  http://192.168.19.81:5000/2210018-d7ae168f/gif.gif</code></p>
<p>拿到文件后拖出来，用一款工具分析，叫做"010Editor"</p>
<p><img class="alignnone size-full wp-image-65" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020220008867.png" alt="" width="961" height="767" /></p>
<p>好的，看到源码可以去世了。</p>
<p>全文件搜一下flag</p>
<p><img class="alignnone size-full wp-image-66" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020220145597.png" alt="" width="871" height="870" /></p>
<p>好的，假的</p>
<p>回头去看一下</p>
<p><img class="alignnone size-full wp-image-67" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020220227786.png" alt="" width="946" height="777" /></p>
<p>看到了<code>39 61</code>。联想一下.gif文件，文件知识：gif的十六进制头标识为：<code>47 49 46 38 39 61</code></p>
<p>尝试一下，编辑补一个头标识保存</p>
<p><img class="alignnone size-full wp-image-68" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020220457956.png" alt="" width="927" height="764" /></p>
<p>果然文件可识别了，真是个gif文件。同时发现了Photoshop修改记录，用Photoshop打开看一下</p>
<p><img class="alignnone size-full wp-image-69" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020220706789.png" alt="" width="1920" height="1039" /></p>
<p>每一帧看一下，得到一串base64，脚本跑解密得到flag</p>
<p><code>Y2F0Y2hfdGh1X2R5bmFtaWNfZmxhZ19pc19xdW10ZV9zaW1wbGU=</code></p>
<p><img class="alignnone size-full wp-image-70" src="http://err0r.top/wp-content/uploads/2020/10/image-20201020221146576.png" alt="" width="1920" height="1030" /></p>
<p>回去交后给flag</p>
<h5>问题解决：</h5>
<p>解密payload：</p>
<pre><code class="language-python" lang="python"># -*- coding: UTF-8 -*-
import base64

a = "Y2F0Y2hfdGh1X2R5bmFtaWNfZmxhZ19pc19xdW10ZV9zaW1wbGU="


decode = base64.b64decode(a)

print(decode)
</code></pre>
<p> </p>
<h2>总结</h2>
<p>一阶段网安课就结束了，这些题目内容都是很有趣的解密，也希望能吸引很多人来了解。总之，只要愿意学，没有解决不了的问题，一起学习吧！如果有兴趣或者想参与可以联系我，不久会举办CTF招新赛，有兴趣可以看看。</p>
<p><a href="http://err0r.top:4100/misc/encode/computer-zh/#base" target="_blank" rel="noopener noreferrer">本站ctfwiki</a></p>
<p><a href="http://err0r.top:8000" target="_blank" rel="noopener noreferrer">本站CTFd平台</a></p>
