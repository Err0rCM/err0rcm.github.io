---
title: Netcat 网络测试工具
urlname: Netcat
date: 2020-11-3 17:57:25
updated: 
comments: false
tags: 
  - netcat
categories: 
  - 杂谈笔记
  - 菜鸡教程
permalink: 
---

**这里是中国科技大学@taoky 写的一篇文章: nc是什么?**
---

请仔细阅读，一般二进制bin方向可能用到nc的地方会很多。



---
nc/ncat 是 netcat 的缩写，它可以读写 TCP 与 UDP 端口——或许你看不懂这句话，这没有关系。简单地说，它可以在字符界面下，让你和服务器沟通交流。
一般来说，有很多题目都会要求你使用 nc 连接到他们的服务器，并且进行交互，获得 flag

## 如何安装 nc？

`nc` 命令在 macOS 中是自带的，在 Linux 中一般自带，或是可以使用相应的软件包管理器安装（如在 Debian/Ubuntu 中这个包名叫 netcat）。

当然，如果你在看这篇手册，你的操作系统很有可能是 Windows。它不自带 `nc`，尽管可以用 WSL 或者虚拟机的方式解决，但是如果你觉得这样太麻烦了，也有另外一些方法。

### 使用静态链接的 ncat 程序

前往 [此处](https://github.com/andrew-d/static-binaries/blob/master/binaries/windows/x86/ncat.exe) (github链接)下载此程序。我们也在这里提供了一份。下载下来即可。

[ncat.zip](/download/ncat.zip)

> 注：nc/ncat 事实上是两个不同的程序，但在我们接下来的使用上，基本没有区别。ncat 是由 Nmap 项目开发的“重置版的 Netcat”。

#### Q:我的杀毒软件报毒怎么办

如果你不相信此来源，也可以下载 nmap（一个网络探测、扫描工具），它会附带一个 ncat。

## 如何使用 nc?

### Windows

直接双击，你会看到一个窗口一扫而过——这是正常现象。你需要使用「命令提示符」或者「PowerShell」来访问这个程序。

Windows 10 中，你可以使用资源管理器 -> 文件来在你存放 nc 的目录中打开命令提示符或 PowerShell。

![](Netcat%20%E7%BD%91%E7%BB%9C%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7/explorer-1024x510.png)
###### 在 Windows 资源管理器中打开命令提示符或 PowerShell

可以直接在`ncat.exe`所在目录，按住`Shift+鼠标右键`点击`在此处打开Powershell窗口`输入`ncat`

或者，你也可以在开始菜单 -> Windows 系统中打开命令提示符，或者在开始菜单 -> Windows PowerShell 中打开 PowerShell，也可以按住`win标+R`弹出`运行`窗口输入`cmd`，然后使用 cd 命令转移到对应的目录：输入 cd 文件夹名 可以让你转移到对应的文件夹，输入 cd .. 可以让你转移到上面一层目录。使用 dir 命令，可以显示当前目录下所有文件。同时，使用 Tab 键可以帮助你补全名称。

当跳转到你存放 nc 的文件夹后：

- 如果你使用的是 PowerShell（蓝色背景），输入 ./ncat（根据 nc 对应的名称不同而不同）。
- 如果你使用的是命令提示符（黑色背景），输入 ncat（根据 nc 对应的名称不同而不同）。

当显示以下内容时，说明你成功运行了它。

![nc](Netcat%20%E7%BD%91%E7%BB%9C%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7/image-20210220172523253.png)
###### 成功运行 ncat


### Linux & macOS

打开「终端」，输入 `nc`。

```
$ nc
usage: nc [-46AacCDdEFhklMnOortUuvz] [-K tc] [-b boundif] [-i interval] [-p source_port] [--apple-delegate-pid pid] [--apple-delegate-uuid uuid]
	  [-s source_ip_address] [-w timeout] [-X proxy_version]
	  [-x proxy_address[:port]] [hostname] [port[s]]
```

出现了类似上面的输出，说明运行成功了。

![image-20210220170815879](Netcat%20%E7%BD%91%E7%BB%9C%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7/image-20210220170815879.png)


### 示例

在我们使用浏览器上网的时候，我们和服务器使用了 HTTP 协议进行连接。关于 HTTP 协议的更多细节，你需要自己上网搜索。一般来说，默认是 80 端口。

我们可以使用 nc，尝试一次与网页服务器的连接，以百度为例。

输入 `nc www.baidu.com 80`（或者 `ncat www.baidu.com 80`，或者 `./ncat www.baidu.com 80`，请根据以上的介绍自行修改），程序会等待你的输入。

输入 GET / HTTP/1.0。这表示，我们使用 HTTP/1.0 这个协议版本，用 GET 的方式请求根 /。输入**两下**回车，代表我们的 HTTP 请求完成。如果你的网络畅通，百度的网页服务器会立刻返回大量信息，可以自行搜索，了解它们的含义。现在，你成功（在不使用浏览器的情况下）完成了一次与百度网站的连接！
![nc success](Netcat%20%E7%BD%91%E7%BB%9C%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7/image-20210220172713535.png)
###### 内容可能不同，返回类似即表示成功

如果你成功了，那么你可以开始愉快地完成我们的题目了！

---
end