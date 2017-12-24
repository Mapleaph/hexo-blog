title: Linux
date: 2015-11-22 10:05:04
categories: Techniques
---

## Ubuntu 登录及注销时自动执行的脚本策略

### 登录时

1. ~/.bash\_profile

2. ~/.bash\_login

3. ~/.profile

按顺序查找，只执行最先找到的那个脚本。

### 注销时

~/.bash_logout

## Hash sum mismatch error while updating Ubuntu

``` bash
sudo rm -rf /var/lib/apt/lists/*
sudo apt-get update
```

## Ubuntu 切换到命令行模式的三种方法

1. sudo init 1;

2. 切换到其他终端：crtl + alt + Fn

3. sudo /etc/init.d/gdm stop

## Authentication failure

Ubuntu 默认不开启 root 帐户，如果使用了 su 命令的话，会提示 authentication failure。

这时，需要用

``` bash
sudo passwd root
```

为 root 帐户设置密码。

## 系统自动发送串口测试指令 (AT+GCAP)

在正常加载驱动后，Ubuntu 的 ModemManager 会自动对串口进行测试，那么就会发现，还没有启动串口就已经有数据从串口发送出去了，要避免这种情况的发生，需要做下面的步骤，将 ModemManager 功能禁用掉。

``` bash
cd /usr/share/dbus-1/system-services
mv org.freedesktop.ModemManager.service org.freedesktop.ModemManager.service.disabled
sudo reboot
```

## Remove unused kernel images

``` bash
uname -r
dpkg --list | grep linux-image
sudo apt-get purge linux-image-x.x.x.x-generic
```

## unknown filesystem: grub rescue

1. ls 列出所有硬盘分区

2. ls (hd0,x)/ 检查分区中是否存在安装的系统

3. 如果找到分区，则:

4. set root=(hd0,x)

5. set prefix=(hd0,x)/boot/grub

6. insmod /boot/grub/i386-pc/normal.mod

7. normal

正常情况下应该进入到启动列表，然后按 c 进入 grub 命令行：

1. set root=(hd0,x)

2. linux /boot/vmlinuz-xxx root=/dev/sda(y)

3. initrd /boot/initrd-xxx

4. boot

如果成功的话就能够正常进入 ubuntu系统了，进入系统后，打开 terminal：

``` bash
sudo grub-install /dev/sda
sudo depmod
sudo update-grub
sudo reboot
```

## vim 方向键变字母的问题

* 方法一

    ``` bash
    sudo apt-get remove vim-common; sudo apt-get install vim
    ```

* 方法二

    ``` bash
    sudo gedit ~/.vimrc
    添加
    set nocompatible
    ```

## Install Boot-Repair on Ubuntu

``` bash
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt-get update
sudo apt-get install boot-repair
```

## configure: error: C++ preprocessor "|lib|cpp" fails sanity check

在编译软件时，如果出现上面错误，在 centos 下运行

``` bash
yum install gcc-c++
```

即可。

## grep lines containing tab character

Using a regular form to grep lines in file.txt containing tab character, be careful about the $ sign before the ‘\t’ expression.

``` bash
grep $'\t' file.txt
```

Using perl script to grep lines in file.txt containing tab character, -P (uppercase) option means perl.

``` bash
grep -P '\t' file.txt
```

Grep lines in file.txt not containing tab character, -v option means reverse.

``` bash
grep -v $'\t' file.txt
```

## grep+find 指定文件范围查找

``` bash
find . -i name "*.c" -exec grep "hello" {} \;
```

## Kernel Panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)

``` bash
sudo initramfs -u -k version
sudo update-grub2
```

## 安装 Nvidia 显卡驱动

1. 禁用 SELinux

2. 将 "blacklist nouveau" 添加到 /etc/modprob.d/blacklist.conf 文件末尾

3. 将 /etc/grub.conf 中的 "nouveau.modeset=0" 去掉

4. 执行 init 3

5. 执行 xxx.run 驱动安装脚本

6. 备份 /etc/X11/xorg.conf 后，将该文件删掉

7. 重新启动系统。

## ssh-keygen 的使用方法

为了让两个 Linux 机器之间使用 ssh 不需要用户名和密码，采用数字签名 RSA 或者 DSA 来完成这个操作。

### 模型分析

假设 A (192.168.20.59) 为客户机器，B (192.168.20.60) 为目标机。

要达到的目的：

A 机器 ssh 登录 B 机器无需输入密码；

加密方式选 rsa 或者 dsa 均可以，默认是 dsa。

### 具体操作流程

#### 单向登陆的操作过程（能满足上边的目的）

1. 登录 A 机器

2. ssh-keygen -t [rsa|dsa]，将会生成密钥文件和私钥文件 id\_rsa, id\_rsa.pub 或 id\_dsa, id\_dsa.pub

3. 将 .pub 文件复制到 B 机器的 .ssh 目录， 并 cat id\_dsa.pub >> ~/.ssh/authorized\_keys

4. 大功告成，从 A 机器登录 B 机器的目标账户，不再需要密码了。（直接运行 ssh 192.168.20.60）

#### 双向登陆的操作过程

ssh-keygen 做密码验证可以使在向对方机器上 ssh, scp 不用使用密码。具体方法如下:

1. 在两个机器上互相执行上面的 1～3 步

2. 设置文件和目录权限：

    ``` bash
    chmod 600 authorized_keys
    chmod 700 -R .ssh
    ```

    要保证 .ssh 和 authorized_keys 都**只有用户自己有写权限，否则验证无效**。

## Ubuntu 10.04 如何进入 grub 命令行

Ubuntu 从 10.04 开始使用 GRUB2.0 版本，默认安装情况下 grub2 启动菜单是隐藏的，除非您改动了 /etc/default/grub 中的设置。

打开 grub2 启动菜单的方法：

在开机启动时一直按 Shift 键，直到 grub2 启动菜单出现，这时候可以按上下方向键选择一个项目，或者直接按 ‘c’ 进入命令行模式。

## 命令行下查找和替换文本的方法

``` bash
sed -i 's/<old-string>/<new-string>/g' `grep <old-string> -rl .`
```

## Getting part of a string

Getting the "File" part

``` bash
echo "File.txt" | cut -d\. -f1
```

Getting the "txt" part

``` bash
echo "File.txt" | cut -d\. -f2
```

**-fn** stands for the nth field

**-d\.** means we use dot character as delimiter. **cut** command uses tab as its delimiter by default.

**-s** option means only shows the results contains the delimiter

## 安装 Grub

``` bash
mount /dev/sdaX /mnt
grub-install --root-directory=/mnt /dev/sdX
```

## gawk

**需要系统中有 troff**

``` bash
./configure
make
cd doc/
make awkward.pdf
```

## wget

### 下载 https 链接

需要添加 --no-check-certificate 选项

### 在配置文件中添加代理

编辑 ~/.wgetrc 文件

``` bash
http_proxy=http://127.0.0.1:8088
https_proxy=http://127.0.0.1:8088
ftp_proxy=http://127.0.0.1:8088
use_proxy=off (默认不开启代理)
```

之后可在命令中用 -Y on 来手动开启代理。

## Bochs

Ubuntu 环境下，默认的配置文件为 /etc/bochs-init/bochsrc.

将配置文件中的

```
romimage: file=$BXSHARE/BIOS-bochs-latest, address=0xf0000
```

改成

```
romimage: file=$BXSHARE/BIOS-bochs-latest
```

即可。

## diff 的四种格式解读

``` bash
# 1. 传统格式
diff <before> <after>

# 2. 上下文格式
diff -c <before> <after>

# 3. 合并格式
diff -u <before> <after>

# 4. git diff
# 这种格式不需要文件位于 git 仓库内
git diff <before> <after>
```

## 几个常用的 RPM 命令

```
1. 查询特定的 rpm 包是否已经安装
rpm -q xxx.rpm

2. 列出系统中所有的 rpm 包，并在中间按关键字查找
rpm -qa | grep xxx

3. 删除制定的 rpm 包
rpm -e xxx.rpm
注：如果有依赖关系的话，默认是不能删除的，这时就要用 yum remove 来进行卸载
```

## 基础命令的进度监控

正常情况下，cp 命令是不会显示进度的，如果你打算拷贝一个 20 GB 的文件，天知道要等多久，心里真没底啊。。。。咋办呢？

下面是方法一：

干脆就不用 cp 命令了，改用下面这个：

``` bash
$ rsync -av --progress <from> <to>
```

但是，别的命令咋办呢，比如 mv？正好在 github 上面有这个项目，[Coreutils Viewer](https://github.com/BestPig/cv)，下载之后 make && make install 就可以了，下面给出使用方法：

``` bash
$ cp <from> <to> & cv -m
```

注意：上面的命令仅仅用了一个 &，用来让 cp 命令在后台执行，而在前台执行 cv -m 命令，目的就是用来监控 cp 的进度，非常方便。

## Ubuntu 内核开发环境搭建

### 配置

* OS：ubuntu-12.04.4-desktop-i386

* 运行环境：虚拟机。内存分配 1GB，CPU 分配一个核心

* 安装配置：磁盘分配为系统默认，默认选择 “安装时下载” 和 “安装第三方软件“

* 系统自带内核：3.11.0-15

* 需要编译的内核：linux-3.11.tar.gz （从 kernel.org 下载）

* 需要 apt-get 安装组件 build-essential, libncurses-dev, kernel-package

### 内核编译及安装步骤

``` bash
cd ~/Desktop; tar zxf linux-3.11.tar.gz; cd linux
make mrproper
make menuconfig
# 界面出来之后直接选择退出，然后选择保存
cp /boot/xxx-config .config
make -j<n> #单核 n=1，双核 n=2
# 接下来会让你确认一个内核选项，是关于 vesa 的，选择 yes 即可
# 然后就进入了编译过程，这里需要漫长的等待，根据上面的虚拟机配置，预计时间为 30 ~ 60 分钟
sudo make modules_install
sudo make install
```

### 新内核使用及相关问题

重启后如果找不到 Grub2 启动菜单，需要在启动时按住 Shift 键，然后在菜单上找到 Previous 选择新内核进入系统后，需要重新安装 VMware Tools，否则无法同主机进行文件共享。编译驱动时，如果提示 popt.h: No such file or directory，需要 apt-get 安装 libpopt-dev。

## 如何验证当前系统是 32 位还是 64 位

方法一

``` bash
$ getconf LONG_BIT
```

方法二

``` bash
$ file /bin/ls
```

## How to Check and Use Serial Ports under Linux

### Check the System Initialization for Serial Ports

``` bash
$ dmesg | grep tty
```

### Check What Serial Ports Your Linux Has

``` bash
$ setserial -g /dev/ttyS[0123]
```

## 串口的简单使用

1. 查看串口设备信息

    ``` bash
    $ dmesg | grep tty
    ```

2. 查看串口驱动信息

    ``` bash
    $ cat /proc/tty/drivers/serial
    ```

2. 读写

    ``` bash
    # read
    $ cat /dev/ttyS1
    # write
    $ echo 12345 > /dev/ttyS1
    ```

## 添加删除用户

1. 注销旧用户

    ``` bash
    $ pkill -u <username>
    ```

    or

    用 ps 命令找到用户登录的终端，然后关闭对应的进程

2. 删除旧用户

    ``` bash
    $ userdel <username>
    ```

3. 添加新用户

    ``` bash
    $ groupadd <groupname>
    $ useradd -g <groupname> -d /home/<username> -s /bin/bash

    # 将用户加入到新组，并不破坏原来的组
    $ usermod -a -G <groupname> <username>
    ```


## 运行软件时提示 GLIBCXX_3.4.15 not found

我打算在 fedora 14 上安装使用 Qt5.4.1，能够正常安装，但是打不开 QtCreator，提示 /usr/lib/libstdc++.so.6: GLIBCXX_3.4.15 not found，在官网上下载了 fedora 15 的 libstdc++-4.6.0-6.fc15.i686.rpm，直接安装的话，会和现有的组件起冲突，可以换另外一种方式：

``` bash
# 对 rpm 进行解压缩
$ rpm2cpio libstdc++-4.6.0-6.fc15.i686.rpm | cpio -div
# 解压出来是一个 usr 文件夹
$ cd usr/lib/
# 可以通过下面的命令查看当前动态库是否包含 GLIBCXX_3.4.15
$ strings libstdc++.so.6.16 | grep GLIBCXX_3.4.15

$ sudo cp libstdc++.so.6.16 /usr/lib/; cd /usr/lib
# 删除旧的链接并重新新建
$ sudo rm libstdc++.so.6
$ sudo ln -s libstdc++.so.6 libstdc++.so.6.16
```

## 修改用户信息

``` bash
$ chfn <username>
```

## Apache 允许外部主机访问

如果安装的是 xampp，在 Apache2.4 以后的版本，需要编辑 /opt/xampp/etc/extra/httpd-xampp.conf，找到 LocationMatch，将里面的 Require local 替换为 Require all granted 即可。

## 网络配置

``` bash
$ ifconfig eth0 192.168.1.120 netmask 255.255.255.0
```

## ELDK 安装

ELDK 是用于生成 u-boot 的开发套件，目前的最新版本是 5.6

下载针对 PowerPC 的镜像 eldk-5.6-powerpc.iso，文件大小 5GB 左右。

``` bash
$ sudo mount -t iso9660 -o loop eldk-5.6-powerpc.iso /mnt
$ cd /mnt/eldk-5.6/
$ sudo ./install.sh -s toolchain powerpc

# 接下来安装会有问题，提示不能读取 toolchain 的 tarball 文件，那么我们就来手动安装

$ cd /opt/eldk-5.6/powerpc
$ sudo mkdir rootfs-minimal
$ sudo tar xpf /mnt/eldk-5.6/targets/powerpc/core-image-minimal-generic-powerpc.tar.gz -C rootfs-minimal

$ sudo mkdir rootfs-minimal-dev
$ sudo tar xpf /mnt/eldk-5.6/targets/powerpc/core-image-minimal-dev-generic-powerpc.tar.gz -C rootfs-minimal-dev

$ sudo mkdir rootfs-minimal-mtdutils
$ sudo tar xpf /mnt/eldk-5.6/targets/powerpc/core-image-minimal-mtdutils-generic-powerpc.tar.gz -C rootfs-minimal-mtdutils

$ sudo mkdir rootfs-minimal-xenomai
$ sudo tar xpf /mnt/eldk-5.6/targets/powerpc/core-image-minimal-xenomai-generic-powerpc.tar.gz -C rootfs-minimal-xenomai
```

### 设置环境变量

``` bash
$ cd /opt/eldk-5.6/powerpc
$ source environment-setup-powerpc-linux
$ export ARCH=powerpc
$ export CROSS_COMPILE=${TARGET_PREFIX}
```

## Ubuntu14.04 安装 Sublime Text 2

``` bash
$ sudo add-apt-repository ppa:webupd8team/sublime-text-2
$ sudo apt-get update
$ sudo apt-get install sublime-text
```

## Terminal 配色方案

文本颜色设为 #708284，背景颜色设为 #07242E

## Ubuntu 用关键字查询已经安装的软件包

``` bash
$ sudo dpkg --get-selections | grep xxx
```

## update-alternatives 学习笔记［转载］

### 作用概述

1. 为多个功能相同但名称不同的软件起一个共用的名称；
2. 可以让同一个软件的多个版本共存。

Linux 发展到今天，可用的软件已经非常多了。这样自然会有一些软件的功能大致上相同。例如，同样是编辑器，就有 nvi、vim、emacs、nano，而且我说的这些还只是一部分。大多数情况下，这样的功能相似的软件都是同时安装在系统里的，可以用它们的名称来执行。例如，要执行 vim，只要在终端下输入 vim 并按回车就可以了。不过，有些情况下我们需要用一个相对固定的命令调用这些程序中的一个。例如，当我们写一个脚本程序时，只要写下 editor，而不希望要为“编辑器是哪个”而操心。Debian 提供了一种机制来解决这个问题，而 update-alternatives 就是用来实现这种机制的。

### 参数详解

1. --display

    它使我们可以看到一个命令的所有可选命令

    ``` bash
    $ update-alternatives --display editor
    ```

    可以看到我的机器上的所有可以用来被 editor 链接的命令。

2. --config

    这个选项使我们可以选择其中一个命令程序来作为 editor

    ``` bash
    $ update-alternatives --config editor
    ```

3. --auto

    update-alternatives 在一般情况下是由 postinst 和 prerm 这样的安装脚本自动调用的，所以一个 alternative 的状态有两种：**自动和手动**。每个 alternative 的 **初始状态都是自动**。如果系统发现管理员手动修改了一个 alternative，它的状态就从自动变成了手动，这样安装脚本就不会更新它了。如果你希望将一个 alternative 变回自动，只要执行代码:

    ``` bash
    $ update-alternatives --auto editor
    ```

4. --install

    ``` bash
    $ update-alternatives --install gen link alt pri [--slave sgen slink salt] ...
    ```

    gen，link，alt，pri 分别在下面解释。如果需要从 alternative，你可以用 --slave。如果你在向一个已经存在的 alternative 组中添加新的 alternative，该命令会把这个 alternative 加入到这个已经存在的 alternative 组的列表中，并用新的可选命令作为新的命令；否则，将会建立一个新的自动的 alternative 组。

5. --remove

    ``` bash
    $ update-alternatives --remove name path
    ```

#### 名词解释

1. general name：这是指一系列功能相似的程序的“公用”名字（包括绝对路径），比如 /usr/bin/editor。
2. link：这是指一个 alternative 在 /etc/alternative 中的名字，比如 editor，--display，--config 和 --auto 后面跟的都是 link。
3. alternative：顾名思义，这是指一个可选的程序所在的路径（包括绝对路径），比如 /usr/bin/vim。
4. 优先级：这个比较简单，当然优先级越高的程序越好啦。
5. 主和从alternative：想想看，你将 /usr/bin/editor 链接到了 vim，可是当你执行 man editor 时看到的却是 emacs 的 manpage，你会做何感想呢？这就引出了主和从 alternative 的概念了：当更新主的 alternative 时，从的 alternative 也会被更新。

## 多个 gcc 共存

因为编译软件的需要，Ubuntu 自带的 gcc4.4.1 版本太高，可能需要 gcc3.x，这里选择的是 gcc3.4.4。

### 下载并安装 deb 安装包

**选择源码包编译安装可能会出错**

下载地址为：http://archive.ubuntu.com/ubuntu/pool/universe/g/gcc-3.4/

1. gcc-3.4-base_3.4.6-6ubuntu3_i386.deb
2. gcc-3.4_3.4.6-6ubuntu3_i386.deb
3. cpp-3.4_3.4.6-6ubuntu3_i386.deb
4. g++-3.4_3.4.6-6ubuntu3_i386.deb
5. libstdc++6-dev_3.4.6-6ubuntu3_i386.deb

### 配置

目前系统里存在有两个版本的 gcc，缺省值为 gcc4.4.1，这里我们需要改变系统的缺省值

``` bash
ls /usr/bin/gcc* -ll
```

```
rwxrwxrwx  1 root root  21      2009-02-10 17:24 /usr/bin/gcc -> /etc/alternatives/gcc
-rwxr-xr-x 1 root root  85552   2008-05-08 18:04 /usr/bin/gcc-3.4
-rwxr-xr-x 1 root root  193372  2008-10-11 03:41 /usr/bin/gcc-4.2
-rwxr-xr-x 1 root root  16090   2008-05-08 17:58 /usr/bin/gccbug-3.4
-rwxr-xr-x 1 root root  2018    2007-06-05 08:59 /usr/bin/gccmakedep
```

### 增加 gcc3.4 和 gcc4.3 可选项

``` bash
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.2 40
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-3.4 30
```

### 切换版本到 gcc-3.4

``` bash
$ sudo update-alternatives --config gcc
```

现有 2 个可选项，它们都提供了 gcc

```
1   /usr/bin/gcc-3.4*
2   /usr/bin/gcc-4.2
```

要维持缺省值 [*]，按回车键，或者键入选择的编号：1

使用 "/usr/bin/gcc-3.4" 来提供 gcc。

## no glxinfo command

``` bash
$ sudo apt-get install mesa-utils
```

## inode

### A Brief Intro

1 sector = 512B = 0.5KiB

1 block contains a series of sectors, commonly 4KiB (8 sectors).

1 disk consists of a data section and a inode section.

1 inode represents 1 file in the disk, it contains

* size in Bytes
* owner ID
* group ID
* RWX priviledge
* timestamp
    * ctime (last inode change time)
    * mtime (last file content change time)
    * atime (last file access time)
* number of links
* data section location

#### Files in Linux

* real file
    * data
    * inode
* directory
    * file list (use the pair (file\_name : inode\_num))
    * inode

The maximum number of files (a.k.a the number of inodes) is fixed after the disk formatting.

### Useful Commands

#### stat

show the inode information of file1:

``` bash
$ stat file1
```

#### df -i

examine the inode usage of the system:

``` bash
$ df -i
```

#### dumpe2fs

check the inode size of disk sda in the current system:

``` bash
$ sudo dumpe2fs -h /dev/sda1 | gerp "Inode size"
```

The result is usually 128B or 256B.

#### ls -i

check the inode number of file1:

``` bash
$ ls -i file1
```

## vmware workstation no 3d support on linux

Open the .vmx file of the virtual machine with a text editor, add the following to the end of the file.

``` bash
mks.gl.allowBlacklistedDrivers = "TRUE"
```

## 简单的网络诊断方法

### 检测网络端口状态

``` bash
sudo netstat -apn
```

### 检测防火墙规则

``` bash
sudo iptables -L
```

### 查看防火墙状态

``` bash
sudo ufw verbose
```