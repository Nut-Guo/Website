---
title: Sciver搞机实录
date: 2019-02-19 01:37:27
categories: 
  - [CTF,基础] 
tags:
  - Manjaro
  - 装机
  - 配置
  - 代理
  - 软件
---
经历了一番骚操作之后不慎干掉了自己经营许久的Manjaro KDE，痛定思痛，重新安装操作系统的过程中必定会踩到许多的坑，小记一波，预防不幸再次发生。

---
安装过程没什么好说的，分区这些东西，总感觉个人用户低端如我，直接一个分区也没什么不好的地方，之后有进一步的需求，例如交换分区的话，swapfile可以成为一个更好的解决方案，从配置的过程开始记起。

---
### 1.换源
>重装千万条，换源第一条；换源不规范，迟早要完蛋。

- 选择国内镜像

```
sudo pacman-mirrors -i -c China -m rank //更新镜像排名
```

- 添加archlinuxcn和arch4edu

```
更改 /etc/pacman.conf
#添加以下内容：
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = http://mirrors.ustc.edu.cn/archlinuxcn/$arch

[arch4edu]
SigLevel = Never
Server = http://mirrors.tuna.tsinghua.edu.cn/arch4edu/$arch
```

- 更换rubygem源

```
# 添加 TUNA 源并移除默认源
gem sources --add https://mirrors.tuna.tsinghua.edu.cn/rubygems/ --remove https://rubygems.org/
# 列出已有源
gem sources -l
# 应该只有 TUNA 一个
```

- git代理

```
git config --global http.proxy 'socks5://127.0.0.1:1080'
```
- 使用以下命令替换 bundler 默认源 

```
bash bundle config mirror.https://rubygems.org https://mirrors.tuna.tsinghua.edu.cn/rubygems 
```
- blackarch repo 的添加与换源

```
# Run https://blackarch.org/strap.sh as root and follow the instructions.
$ curl -O https://blackarch.org/strap.sh

# The SHA1 sum should match: 9f770789df3b7803105e5fbc19212889674cd503 strap.sh
$ sha1sum strap.sh

# Set execute bit
$ chmod +x strap.sh

# Run strap.sh
$ sudo ./strap.sh 
换源
$ kate /etc/pacman.conf
#修改
[blackarch]github下载文件巨慢，挂代理(先配置好socks5)
[方法](https://www.jianshu.com/p/5e74b1042b70)

SigLevel = Optional TrustAll
Server = https://mirrors.ustc.edu.cn/blackarch/$repo/os/$arch
注意及时安装相应的keyring
```

还有换源问题的话求助tuna好了
https://mirror.tuna.tsinghua.edu.cn/

---

### 2.必要软件的安装
- yay(aur软件安装)
- yaourt
有时候aur源下载巨慢，可考虑通过将下载好的文件放到yaourt的tmp文件中，并通过yaourt更改PKGBUILD文件指向本地安装包，跳过校验

- [electron-ssr](https://github.com/erguotou520/electron-ssr)

- [搭梯子](http://www.frogjun.com/fq-a/)

问题 ：electron-ssr无法使用

方案：版本适配问题，目前(2019-2-12)0.2.6版本对KDE版本存在bug，安装之前的版本，手动设置开机启动,浏览器之外的软件使用ssr，配置socks5代理即可，如telegram

问题：终端无法科学上网

方案：[proxychains](https://blog.fazero.me/2015/08/31/%E5%88%A9%E7%94%A8proxychains%E5%9C%A8%E7%BB%88%E7%AB%AF%E4%BD%BF%E7%94%A8socks5%E4%BB%A3%E7%90%86/)

问题：github下载文件巨慢，挂代理(先配置好socks5)

方法：[配置socks5代理](https://www.jianshu.com/p/5e74b1042b70)

- 搜狗输入法

```
sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-im
sudo pacman -S fcitx-configtool#kde桌面环境下的kcm-fcitx更为美观)
sudo gedit ~/.xprofile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="im=fcitx"
```

- zsh

```
sudo pacman -S zsh zsh-completions
cat /etc/shells
chsh -s /bin/zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

配置zsh主题(修改~/.zshrc)，推荐steeef，此外theunraveler,af-magic

- goldendict
[字典库](https://www.zhihu.com/question/21133533/answer/142164875)

- vmware

问题：vmware无法运行虚拟机

方案：安装linux-headers

问题：vmware无法联网

方案：启用vmware-net服务

```
systemctl start vmware-networks.service
systemctl enable vmware-networks.service
```

问题：vmware图形加速

方案：修改配置文件~/.vmware/preferences
增加或修改为

```
mks.gl.allowBlacklistedDrivers = "TRUE"
```

问题：交换空间不够

方案：参见5，设置swapfile
- crossover

问题：crossover 缺少so文件

方案：安装相应的lib32文件

- telegram(注意配置socks5端口)

- peda

```
echo "source /usr/share/peda/peda.py" >> ~/.gdbinit 
```
- tmux[(配置)](https://github.com/izuolan/.tmux)

工具

- radare2+cutter

最终还是没能解决jupyter的问题。。。

下载之后安装decompiler插件
```
$r2pm -i r2dc
```

补充:(后来发现radare2本身足够好用，cutter并非必需品)

[入坑传教](https://github.com/ITAYC0HEN/A-journey-into-Radare2)

喜欢的配色!!!：cga

- ida[下载](https://pan.baidu.com/s/1c3R6iZY#list/path=%2F)
- [hopper](https://www.sendspace.com/file/dpjvwd)

---
### 3.各类放缩问题
- 系统缩放设置
- 搜狗拼音外观设置更改字体大小
- crossover更改wine的dpi

---
### 4.挂载Windows分区
查看Windows分区UUID
```
$ lsblk -f

NAME   FSTYPE LABEL      UUID                                 MOUNTPOINTL
sda                                                         
├─sda1 ext4   Arch_Linux 978e3e81-8048-4ae1-8a06-aa727458e8ff /
├─sda2 ntfs   Windows    6C1093E61093B594                     
└─sda3 ext4   Storage    f838b24e-3a66-4d02-86f4-a2e73e454336 /media/Storage
sdb                                                           
├─sdb1 ntfs   Games      9E68F00568EFD9D3                     
└─sdb2 ext4   Backup     14d50a6c-e083-42f2-b9c4-bc8bae38d274 /media/Backup
sdc                                                           
└─sdc1 vfat   Camera     47FA-4071                            /media/Camera
```
修改/etc/fstab文件
```
# <file system>   <dir>		<type>    <options>             <dump>  <pass>
UUID=(分区的UUID)  /mnt/windows(挂载点)  ntfs-3g   defaults		  0       0
```

---
### 5.设置swapfile
- 手动设置
[(官方wiki)](https://wiki.manjaro.org/index.php?title=Add_a_/swapfile)

- 自动化
 systemd-swap

[安装](https://wiki.archlinux.org/index.php/Install "Install") 软件包 [systemd-swap](https://www.archlinux.org/packages/?name=systemd-swap)。
设置 `/etc/systemd/swap.conf` 文件的 *Swap File Universal* 一节下的 `swapfu_enabled=1`。 [启动/启用](https://wiki.archlinux.org/index.php/Start/enable "Start/enable") `systemd-swap` 服务。 访问 [作者的 GitHub](https://github.com/Nefelim4ag/systemd-swap) 页面来获取更多的信息，或者设置 [推荐的配置](https://github.com/Nefelim4ag/systemd-swap/blob/master/README.md#about-configuration)。

---
### 6.odoo
想自己动手改善果壳生活体验，遂开始折腾odoo！
如果有包管理的话通过包管理安装自然最好，否则可考虑源码安装
[官方文档](https://www.odoo.com/documentation/master/setup/install.html)
[postgresql wiki](https://wiki.archlinux.org/index.php/PostgreSQL)
此处吸取教训，官方指南总是比博文要来的规范，能靠官方文档解决的问题不求人
module没有加载的真实原因不是版本不对，而是自己没有把步骤都做完呀。。。
```shell
#安装
yay -S odoo
sudo pacman -S postgresql
#配置数据库
sudo -iu postgres
initdb -D /var/lib/postgres/data
```

### 7.Linux 与Win10的交易

时间同步

`sudo timedatectl set-local-rtc true`

[公用蓝牙鼠标](https://zhuanlan.zhihu.com/p/31640193)



