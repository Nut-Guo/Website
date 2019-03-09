---
title: Bandit整理
date: 2019-02-21 23:25:20
tags: Wargame
categories: -[CTF,刷题笔记]
---

题目来源：[OverTheWire](http://overthewire.org/wargames/)

# Bandit0

- ssh

  ssh <username>@<remoteserber> -p <port>

# Bandit1

[linux命令](https://www.cnblogs.com/peida/archive/2012/12/05/2803591.html)

或者你可以自己man

dashed filename：-会被识别为标准输入输出，若存在名称为“-”的文件，则访问时需要使用./-或者绝对路径

[special characters](http://tldp.org/LDP/abs/html/special-chars.html)

# Bandit2

包含空格的文件名，用' '或者" "括起来

# Bandit3

通过ls -la查看所有的文件，其中隐藏文件以.开头

# Bandit4

存档：pIwrPrtPN36QITSp3EQaw936yaFoFgAB

文件名以"-"开头的在表示文件名时应当包括括号或者在前面加"./"

# Bandit5

存档：koReBOKuIDDepwhWk7jZC0RTdopnAYKh

find 命令，

-type 查找文件类型

-size n[bckw]

-exec command {} \;

-executable

加!表示对后面一条参数制定的内容取反

# Bandit6

DXjZPULLxYr17uwoI01bNLQbtFemEgo7

find命令

-user 用户

-group 组

# Bandit7

HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

cat filename | grep content 在指定文件中查找内容

# Bandit8

cvX2JJa4CFALtqS87jk27qwqGhBM9plV

sort 文件中内容排序

| 管道

uniq -u 查找文件中仅仅出现了一次的行

# Bandit9

UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

strings 显示文件中的字符串

# Bandit10

truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

base64 命令利用base64对文件解码

# Bandit11

IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

tr 命令　translate 将两个集合中的元素做相应的替换

# Bandit12

5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

这题折腾了各种解压方式

gzip文件，需要先利用mv命令将文件的后缀名改为.gz然后使用 gunzip filename 来解压

bzip2文件，一般以.bz2为后缀，可以通过bzip2 -d filename 来解压缩

tar 文件，使用tar xvf filename 来解压缩

同时，还接触到了xxd命令，该命令可以将十六进制的文本文件还原为可执行的二进制文件

xxd -r filename > destinationname

# Bandit13

8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL

远程复制　scp -P port username@location:filepath destination

利用秘钥的远程登录　ssh -i key username@location:filepath -p port

# Bandit14

4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e

nc命令从给定ip地址的指定端口获取文件: nc address port

利用echo 命令传递密码

# Bandit15

BfMYroe26WYalil77FoDi9qh59eK5xNr

```bash
openssl s_client -ign_eof -connect localhost:30001
```

s_client 表示作为一个客户端，-ign_eof 表示忽略eof，在此处的用途是使中断在读取到我们输入的密码末尾的eof后仍然保持连接（然而我在实测过程中发现不加这个参数似乎也名没有发现任何异常）。

-connect localhost:30001声明了链接的ip和端口

可使用echo password| 来通过管道实现密码的输入

# Bandit16

cluFn7wTiGryunymYOu4RcffSxQluehd

通过nmap扫描端口，并尝试和排除其中的允许其他协议的端口

nmap -p port1-port2 address

# Bandit17

利用diff 命令比较两个文件

diff file1 file2

输出中的a为add，c为change，d为delete的记号

<后是file1中的数据

\>后是file2中的数据

# Bandit18

kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd

这个挑战中.bashrc中的设置使伪终端pty(pseudo terminal)bash在打开后被自动关闭

为了克服这个问题，我们需要使用伪终端，通过在ssh 命令中使用-t强制分配伪终端，或者是利用-T命令禁止使用伪终端，从而避免了.bashrc发挥作用

# Bandit19

IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

利用所给的二进制文件来更改uid，从而获得其提供的权限，打开制定位置的密码。

chmod 中可以通过u+s 或者g+s 来更改suid权限，这个十分危险，将赋予二进制文件root权限。

# Bandit20

GbKksEFF4yrVs6il55v6gwY5aVje5f0j

建立监听端口

nc -l -p "port: < "filepath"

或者利用echo

echo "content" | nc -l -p "port"

同时对于nc命令，可以利用添加&参数使命令在后台运行

# Bandit21

gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

查看位于/etc/cron.d目录下的文件发现守护进程，得到了一个脚本（.sh文件）在该文件中我们发现密码被导入了一个新建的文件中，查看该文件获得密码



# Bandit22

Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI

命令执行后在后方加上>/dev/null 将输出导入到了/dev/null，而该文件夹会无视所有的输入，从而达到了忽视输出的效果

使用守护进程中利用了cut 命令剪切了md5sum加密的一段字符，将其作为文件名(cut -d "分隔符" -f "选取第几段")

我们重新相应的执行该过程获得文件名

# Banidt23

jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n

cron 的进程每隔一段时间就会被执行，通过前几关相同的办法，我们得到了执行的脚本，解读后我们发现它会执行目录下的所有脚本，我们可以写自己的脚本，并将其放到题目中声明的文件夹中，从而使之被执行（利用其权限获得用户密码）

# Bandit24

UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

























