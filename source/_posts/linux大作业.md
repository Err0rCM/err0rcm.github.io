---
title: linux大作业
comments: false
hide: false
date: 2019-12-15 19:34:13
urlname: linuxprogramme
updated: 
permalink: 
tags: 
categories: Programme
password: gyycoming
---

Linux C环境编程的题目，防止抄袭，CSDN上的我删掉了部分代码，这里放出完整版本



<!-- more -->





﻿## 《Linux C编程环境》 课程大实验 及近期练习题：计算器，复写机，目录树创建，批处理执行器，扫雷



----

​			之前作业的题了，征求了老师的意见，同意我把个人解析放开公布，再提交作业已经无效了。网上可**搜不到**这些题。

----

## 问题名称：计算器

### 交互场景：

程序读input.txt文件，逐行完成文件中计算指令，将每一步的执行结果输出到output.txt文件中去。



#### Input.txt文件内容举例：

```
123

+

234

*

2

```



#### 对应的output.txt文件内容举例：

```
357

714

```

#### 思路过程：

C语言最后一章讲了数据持久化——文件，不知道同学们有没有思考下何谓数据持久化？这也是文件的意义。

首先定义文件指针fpr来读取文件，fpw来输出文件，并用标准格式fopen打开，创建文件。失败则有提示语句并退出。

这里注意，题目要求是**当前路径**下！带盘符这样的路径是**绝对路径**，你无法保证在其他电脑上运行时，在相应的路径下有一个input文件，所以这里用**相对路径**！就是在程序**运行时**的目录下！不需要带盘符等东西。

```c
	FILE *fpr,*fpw;
    char str[20];
    int s=0;
    if((fpr=fopen("input.txt","r"))==NULL)//读取不需要写入，所以以只读打开
    {
        printf("Can't open!'");
        exit(-1);//使用exit函数需要加头文件<stdlib.h>
    }
    if((fpw=fopen("output.txt","w"))==NULL)//输出要以写的方式创建文件
    {    
        printf("Can't write!'");
        exit(-1);
    }

```

接着定义字符串数组str和统计的数s，由于题目是计算器，input**肯定是**第一行**一个数**，第二行**一个运算符**再第三行**一个数**，以此类推，所以我构造了cal函数，里面用switch开关语句来决定进行何种运算。

```c
int cal(char c,int s,FILE *fpr)//c是传入的运算符，表明要做何种运算，s是计算结果
{
	char str[20];//用来存文件再向下读一行读到的数字
	switch(c)//读到了运算符，再往下读一定是数字！
    {
        case '+':fgets(str,20,fpr);s+=change(str);break;
        case '-':fgets(str,20,fpr);s-=change(str);break;
        case '*':fgets(str,20,fpr);s*=change(str);break;
        case '/':fgets(str,20,fpr);s/=change(str);break;
    }
    return s;//返回值即为计算结果
}

```

我们看，第一次是一个数，第二次是一个符号，第三次又是一个数，第四次又是一个符号，我们很容易发现，其实它是第一次一个数，然后就是一个符号一个数，一个符号一个数，于是我们**单独处理**第一个数，后面就是循环了。

先读取了input第一行，进行了加运算，给s一个初值，因为第一个是单独的数，我们就可以等价于原结果s为0，第一次运算就是**0+第一行**。

`         s=cal('+',s,fpr);  `//加运算，s初值为0，返回值即为第一个数了

然后进行while循环，因为文件位置的指针到了下一行，再读取**一定是**一个运算符，当读取到字符不是NULL说明没结束就继续循环，

`         while(fgets(str,sizeof(str),fpr))  `

读到运算符那一行在字符串str里，现在str里有什么？

```
str[0]='(一个运算符)'
str[1]='\n'//换行会被读入
str[2]='\0'
```

调用cal函数**传递读到的运算符**str[0]，**switch**开关进行计算并写入文件，最后fputc加一个换行，至此循环体结束。

```c
	fprintf(fpw,"%d",s=cal(str[0],s,fpr));
    fputc('\n',fpw);
```



其中计算时还用到一个小自定义的函数change，就是将字符串内容读取并转换成整形return。在cal函数里switch判断做什么运算后再进行一次读取，然后传入change，返回整形的这个数。

```c
int change(char s[])
{
    int a=0,i=0;
    while(s[i]!='\n')//换行会被读入，千万不要写s[i]！='\0'！
    {
        a*=10;
        a+=s[i++]-'0'; 
    }
    return a; 
}

```

这样程序就完成了，最后fclose关闭文件。



#### Example Answer:

```c
#include<stdio.h>
#include<stdlib.h> 
int change(char s[])
{
    int a=0,i=0;
    while(s[i]!='\n')
    {
        a*=10;
        a+=s[i++]-'0'; 
    }//理解如何字符串转数字，也可以用sscanf函数
    return a; 
}
int cal(char c,int s,FILE *fpr)
{
	char str[20];
	switch(c)//进行对应的运算
    {
        case '+':fgets(str,20,fpr);s+=change(str);break;
        case '-':fgets(str,20,fpr);s-=change(str);break;
        case '*':fgets(str,20,fpr);s*=change(str);break;
        case '/':fgets(str,20,fpr);s/=change(str);break;//再读取就一定读到了数字
    }
    return s;
}
int main()
{
    FILE *fpr,*fpw;
    char str[20];
    int s=0;
    if((fpr=fopen("input.txt","r"))==NULL)
    {
        printf("Can't open!'");
        exit(-1);
    }
    if((fpw=fopen("output.txt","w"))==NULL)
    {    
        printf("Can't write!'");
        exit(-1);
    }
    s=cal('+',s,fpr);//相当于+运算
    while(fgets(str,sizeof(str),fpr))//这边读一定是读到字符
    {
        fprintf(fpw,"%d",s=cal(str[0],s,fpr));//把字符传过去
        fputc('\n',fpw);
    }
    fclose(fpr);
    fclose(fpw);
    return 0;
}

```



----

## 问题名称：复写机

### 交互场景：

程序读取用户的输入，是一个整数n。程序读取input.txt文件，将文件中的每个字符复写n次后，作为output.txt文件的内容写出。

#### 键盘输入举例：

2

#### Input.txt文件内容举例：

```
Abc

Efg
```

#### 对应的output.txt文件内容举例：

```
AAbbcc

EEffgg
```



#### 思路过程：

人类的本质是复读机！

这题比第一题简单。与第一题一样，定义文件指针fpr和fpw，读取和创建输入输出文件，失败则有提示语句并退出。

先定义n，题目要求**键盘输入**n，然后还是while循环，当fgets读取没返回NULL表示没结束就继续循环，读取到内容给字符串数组str，

`  while(fgets(str,sizeof(str),fpr))`

然后**构造函数write**，传入数组str，文件指针fpw来输出，还有输入的n，

`void write(char str[],FILE *fpw,int n)`

因为我们已经读了一行内容放入字符串str，所以不必传fpr了，然后在write函数里，while循环，读取str每个字符，只要不是换行符就继续循环，

```c
	int c,j=0,i;
    while((c=str[j++])!='\n')

```

然后for循环，输出n次读到的字符到output，结束后没有返回值，

```c
	for(i=0;i<n;i++)
        fputc(c,fpw);

```

回到主函数，输出一个换行符，至此循环体结束，这样就能一行一行读取str，再一个一个输出字符n次，最后fclose关闭文件，程序就完成了。

没错，就这么简单



#### Example Answer：

```c
#include<stdio.h>
#include<stdlib.h> 
void write(char str[],FILE *fpw,int n)//传来了n
{
	int c,j=0,i;
    while((c=str[j++])!='\n')//对于每一个字符，注意不是！='\0'上面解释过
		for(i=0;i<n;i++)
       		fputc(c,fpw);//输出n次
}
int main()
{
	FILE *fpr,*fpw;
	int n;
	char str[50];
	if((fpr=fopen("input.txt","r"))==NULL)
    {    
        printf("Can't open!'");
        exit(-1);
    }
    if((fpw=fopen("output.txt","w"))==NULL)
    {    
        printf("Can't write!'");
        exit(-1);
    }
    scanf("%d",&n);
    while(fgets(str,sizeof(str),fpr))
    {
   	 	write(str,fpw,n);//调用函数
		fputc('\n',fpw);//输出个换行
	}
	fclose(fpr);
	fclose(fpw);
	return 0;
}

```



----

## 问题名称：目录树创建

### 交互场景：

程序读取用户输入的整数n，在**当前目录**下**创建n个子目录**，**目录名依次为“dirx”**，其中x为1-n的某个数字，**每个**目录中**均有一个**readme.txt文件，文件中的**内容均为x这个数字**。



#### 没得样例



#### 思路过程：

本题是Linux C的题，不再仅仅是C语言的题了。一样先定义文件指针fp，不过只要定义一个输出指针就行了，因为不需要读取文件，一样失败会有提示语句并退出。

首先先输入n，要创建n个子目录名字为dirx，定义字符串path表示创建的目录名，定义字符串name表示创建目录里readme.txt的名字

因为要创建n个子目录，所以就写个for循环做n次，用mkdir函数，内跟路径名和权限代码，windows下不起作用，但Linux操作系统这里用0777表示所有人都有对文件读写执行的权限。路径名path，我们赋了三个字符”dir”，因为最后一位就是1-n顺序的数，所以刚好把for循环的i用上，

```c
int n,i;
scanf("%d",&n);
char name[20],num[10];
for(i=1;i<=n;i++)
{
	char path[10]="dir";
...未完
```

我首次写代码这里只写了把i赋给path[3],即`path[3]=i+'0'`//注意字符串的0不是数字0

可是后来想到了问题，如果输入的数不是一位数，而是两位数三位数，这里就会出错了(不可能一位里面存两位数)

于是我想到了函数sprintf，将int型数i转换为字符串类型

`sprintf(num,"%d",i);`

然后strcat连接path，这样path就是”dirx”了，x为顺序的数，每次循环都随着i变化，用mkdir函数路径为path建立子文件夹就好了。

```c
strcat(path,num);//num看上面，是i的数字字符
mkdir(path,0777);//在path目录下建一个dirx的文件夹
//因为这里都是在for循环里的！（看上面）

```

然后要创建文件了，在每个目录底下，于是用strcpy函数先把path拷到name里，再用strcat函数补上”/readme.txt”，这样字符串name里就有”dirx/readme.txt”，'/'表示进目录里面

```c
strcpy(name,path);//此时name="dirx"
strcat(name,"/readme.txt");//此时name="dirx/readme.txt"

```

这时候再fopen创建文件路径为name，把num也就是那个x输出到文件里，最后fclose，

```c
	if((fp=fopen(name,"w"))==NULL)
      {    
           printf("Can't write!'");
           exit(-1);
      }
      fputs(num,fp);//这里不用转为数字，因为即使输出数字字符我们在文件里看到的也是这个数字
      fclose(fp); 

```

这些都在一个循环里，至此循环体结束，这样做n次循环，也就达到了题目的要求。

我做出修改的就是这题，这里要注意字符串函数的应用，同时要考虑程序会不会类似有bug的情况发生！



#### Example Answer：

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
int main()
{
	FILE *fp;
	int n,i;
	scanf("%d",&n);
	char name[20],num[10];
	for(i=1;i<=n;i++)//在循环内，一次循环就创建一个文件夹，文件，输入数字
	{
		char path[10]="dir";//每次都是重赋dir
		sprintf(num,"%d",i);//把i读取成字符
		strcat(path,num);
		strcpy(name,path);
		strcat(name,"/readme.txt");//注意名称的连接
		mkdir(path,0777);//创建文件夹函数
		if((fp=fopen(name,"w"))==NULL)//name字符串里就是目标目录
      	{    
           printf("Can't write!'");
           exit(-1);
     	}
      	fputs(num,fp);
      	fclose(fp); 
	}
	return 0;
}

```

----

## 问题名称：批处理执行器

### 交互场景：

程序读取cmd.txt文件，将cmd.txt文件中的每行执行的结果收集到output.txt文件中。

#### Cmd.txt文件内容举例：

```
pwd

ls | wc -w
```

#### 对应的output.txt文件内容举例：

```
/home/ne/

8
```



#### 思路过程：

批处理执行器，也是Linux C的题目，有读取有输出，定义文件指针fpr和fpw，报错有提示语句并退出，这里多定义一个空文件指针temp，因为接下来要用到**popen**函数。定义字符串contect来**读取cmd执行的结果**，定义字符串str读取cmd.txt的行内容。

```c
	FILE *fpr,*fpw,*temp;
    char contect[100],str[100];
    if((fpr=fopen("cmd.txt","r"))==NULL)
    {
        printf("Can't open!'");
        exit(-1);
    }
    if((fpw=fopen("output.txt","w"))==NULL)
    {    
        printf("Can't write!'");
        exit(-1);
    }

```

写一个while循环，只要fgets读到不是NULL就继续，然后用popen开管道调用fork（）产生子进程，执行shell运行之前读取到str里的命令，把结果读取到空指针temp中

然后fgets读取temp到字符串connect里，这里connect就是cmd运行的结果了，我们fputs直接写入output文件中，然后pclose**关闭管道**

```c
	while(fgets(str,sizeof(str),fpr))
    {
      temp=popen(str,"r");
      fgets(contect,sizeof(contect),temp);
      fputs(contect,fpw);
      pclose(temp);
	}

```

至此循环体结束，最后fclose关闭文件，问题就解决了。

如果不明白popen的用途可以看书第七章（我其实也没怎么看书，只是把函数都过一遍，然后要用啥就去看它的函数原型），或者网上搜popen函数学习。



#### Example Answer：

```c
#include<stdio.h>
#include<stdlib.h>
int main()
{
	FILE *fpr,*fpw,*temp;
    char contect[100],str[100];
    if((fpr=fopen("cmd.txt","r"))==NULL)
    {
        printf("Can't open!'");
        exit(-1);
    }
    if((fpw=fopen("output.txt","w"))==NULL)
    {    
        printf("Can't write!'");
        exit(-1);
    }
    while(fgets(str,sizeof(str),fpr))
    {
      temp=popen(str,"r");//temp里指向类似临时文件，里面存了shell运行结果
      fgets(contect,sizeof(contect),temp);//读取出来
      fputs(contect,fpw);//输出文件
      pclose(temp);//注意每次循环内要pclose
	}
	fclose(fpr);
    fclose(fpw);
    return 0;
}

```





----

### 作业题结束了，下面另外的题了，之前有位同学问我的，我觉得比较有趣，就拿来分享一下。

----

## 问题名称：扫雷

### 问题描述：

 扫雷游戏是一款十分经典的单机小游戏。在nn行mm列的雷区中有一些格子含有地雷（称之为地雷格），其他格子不含地雷（称之为非地雷格）。玩家翻开一个非地雷格时，该格将会出现一个数字——提示周围格子中有多少个是地雷格。游戏的目标是在不翻出任何地雷格的条件下，找出所有的非地雷格。

现在给出nn行mm列的雷区中的地雷分布，要求计算出每个非地雷格周围的地雷格数。

注：一个格子的周围格子包括其上、下、左、右、左上、右上、左下、右下八个方向上与之直接相邻的格子。

#### 输入格式

第一行是用一个空格隔开的两个整数nn和mm，分别表示雷区的行数和列数。

接下来nn行，每行mm个字符，描述了雷区中的地雷分布情况。字符’*’表示相应格子是地雷格，字符’?’表示相应格子是非地雷格。相邻字符之间无分隔符。

#### 输出格式

输出文件包含nn行，每行mm个字符，描述整个雷区。用’*’表示地雷格，用周围的地雷个数表示非地雷格。相邻字符之间无分隔符。 

####  样例输入 #1 

3 3
\*??
???
?*?

#### 样例输出 #1 

\*10
221
1*1

#### 样例输入 #2 

2 3
?*?
*??

#### 样例输出 #2

2*1

*21

#### 说明/提示

对于 100\\%100%的数据， 1≤n≤100, 1≤m≤1001≤n≤100,1≤m≤100。 



#### 思路过程：

​		准备用一个主函数解决，看输入，先输入两个数定义雷区长宽，然后输入雷区数据，由于是符号'?'和'*'，所以定义字符串数组s，行为n，列为m+1，因为要放'\0'。然后为了计算一个格子周围8个格子的雷数，我们定义一个整形数组a，把周围包起来，也就是行为n+2，列为m+2。

```c
	int i,j,n,m,x,k,t;
    scanf("%d %d",&n,&m);
    int a[n+2][m+2];
    char s[n][m+1];
```

例如样例输入1的

```
*??
???
?*?
```

在字符串数组s里就是

```
*??\0
???\0
?*?\0
```

先把数组a全置为0，然后对于有雷的地方我们在数组a里把它置为1

```c
	for(i=0;i<n+2;i++)
    	for(j=0;j<m+2;j++)
    		a[i][j]=0;
    
    for(i=0;i<n;i++)
        for(j=0;j<m;j++)
            if(s[i][j]=='*')
                a[i+1][j+1]++;
```

对于样例输入1数组a里内容

```
00000
01000
00000
00100
00000
```

真正中间3x3才是雷区，周围一圈是包起来的

然后开始运算，对于中间的3x3每个格子，如果是1，那就不动；如果是0，那就要计算雷数，这样就是对于它周围八个格子，如果有1(表明有雷)，计数的x就自增，字符串s里就输入这个位置的雷数

这也是为啥数组a要包一圈雷区

```c
	for(i=0;i<n;i++)
    	for(j=0;j<m;j++)
    	{
        	if(a[i+1][j+1]==1)
        	    continue;
        	x=0;//每次循环置为0
        	for(k=0;k<=2;k++)
        	    for(t=0;t<=2;t++)
        	        if(a[i+k][j+t]==1)//这边其实是i+1+k，-1<k<1。j同理。
        	            x++;
        	s[i][j]=x+'0';
    	}
```


然后输出就行了

题目就完成了



#### Example Answer：

```c
#include <stdio.h>
#include<string.h>
int main()
{
    int i,j,n,m,x,k,t;
    scanf("%d %d",&n,&m);
    int a[n+2][m+2];
    char s[n][m+1];
    getchar();//上面有一次scanf输入，要吃掉一个换行符，不然下面输入会有换行符到s[0][0]
    for(i=0;i<n;i++)
    	gets(s[i]);//输入雷区数据
    for(i=0;i<n+2;i++)
    	for(j=0;j<m+2;j++)
    		a[i][j]=0;//数组a置0
    for(i=0;i<n;i++)
        for(j=0;j<m;j++)
            if(s[i][j]=='*')//雷点置为1
                a[i+1][j+1]++;
	for(i=0;i<n;i++)
        for(j=0;j<m;j++)
        {
            if(a[i+1][j+1]==1)//如果是1，表明这地方是雷
                continue;//对于s就不动它，结束本层循环
            x=0;//计数器置0
            for(k=0;k<=2;k++)
                for(t=0;t<=2;t++)
                    if(a[i+k][j+t]==1)//周围8格扫雷
                        x++;//有雷就自增
            s[i][j]=x+'0';//把雷数的数字字符覆盖掉s内的'?'
        }
    for(i=0;i<n;i++)//最后输出
	{	
		for(j=0;j<m;j++)
			printf("%c",s[i][j]);
		putchar('\n');	
    }
    return 0;
}
```



----

----

本来不想写的，可是马上要编程竞赛了，又凑上期末考试，就抽时间发博客了，刚好自己也复习巩固一下算法

看了下历年算法比赛，就六道题，17年还会三道，18年不会了，保佑我今年算法比赛混个奖

祝大家期末考试都取得好成绩！

 如果有问题或者错误请见谅，并联系我改正！一起努力学习！ 
