title: Git
date: 2015-11-21 22:22:53
categories: Techniques
---

Git 是基于分支概念的

**所有的版本控制系统，都只能跟踪文本文件（也就是说，不能是）的内容变化，像图片，视频，word 文档这些二进制文件，版本控制系统只能记录下文件大小的变化，不能跟踪内容。**

## 版本回退后的撤销

**要点**

要有 commit\_id 就能回到对应的版本

``` git
git reflog
```

## 标签的使用

标签的作用，即为某个我们认为具有特殊意义的提交创建一个别名指针（如：v1.0），这样，该提交就可以很方便的被我们定位，而不需要记住它的 commit\_id（如：efa123f）试想一下，那会有多难。

### 创建

``` bash
为最新的提交打标签
$ git tag v1.0

为指定提交打标签
$ git tag v1.0 <commit_id>

打标签并附带说明文字
$ git tag -a v1.0 -m "descriptions"

附加 GPG 私钥签名（需要安装 GPG）
$ git tag -s v1.0 <commit_id>

将指定标签推送到远程仓库
$ git push origin v1.0

(上方对比分支推送)
($ git push -u origin master)

将所有标签推送到远程仓库
$ git push origin -- tags
```

### 显示

``` bash
显示左右标签，按字母顺序排序
$ git tag

显示标签详情
$ git show v1.0
```

### 删除指定标签

``` bash
在没推送到远程仓库的情况下删除
$ git tag -d v1.0

同时删除本地和远程仓库标签
$ git tag -d v1.0
$ git push origin :refs/tags/v1.0
```

## 让 git status 支持中文字符

如果使用 UTF-8 字符集

``` bash
git config --global core.quotepath false
```

如果使用 GBK 字符集

``` bash
git config --global i18n.logOutputEncoding gbk
git config --global i18n.commitEncoding gbk
```

## git diff 对象解读

1. 100644 表示普通文件

2. 100755 表示可执行文件

3. 120000 表示符号链接

## Submodule

### 在当前工程中添加 Submodule

``` bash
$ git submodule add https://github.com/xxx/xyz.git xyz
$ git add xyz .gitmodules
$ git commit -m 'add submodule xzy'
$ git push origin master
```

### Clone 一个带有 Submodule 的工程

``` bash
$ git clone https://github.com/yyy/abc.git; cd abc
# 这时候可以看到 abc 目录中有 xyz 子目录，但是这个子目录是空的，我们需要手动进行 Submodule 初始化来获取内容
$ git submodule init
$ git submodule update
```

## git 服务器搭建

服务器端的配置：

``` bash
# make sure that the git's ssh permission is correct.
git@xxx$ chmod 700 /home/git/.ssh
git@xxx$ chown git:git /home/git/.ssh
git@xxx$ chmod 644 /home/git/.ssh/authorized_keys
git@xxx$ chown git:git /home/git/.ssh/authorized_keys

# 安装必要组件
$ sudo apt-get install python-setuptools

# 添加新用户 git
$ sudo useradd -m git
$ sudo groupadd git
$ sudo passwd git

# 安装 gitosis
$ cd /home/git/
$ git clone https://github.com/tv42/gitosis.git
$ cd gitosis
$ sudo python setup.py install

# 将 gitosis 管理员的公钥导入并进行 gitosis 初始化
$ sudo -H -u git gitosis-init < /path/to/id_rsa.pub
$ sudo chmod 755 /home/git/repositories/gitosis-admin.git/hooks/post-update

# 新建 git 仓库并初始化
$ mkdir ~/repositories/testproj.git
$ cd ~/repositoreis/testproj.git
$ git init --bare
```

如果想要重新创建管理员，只需要将 repositories 下的 gitosis-admin.git 删除，之后重新执行初始化即可。

客户端配置：

``` bash
# 配置 gitosis
$ git clone git@<server-ip>:gitosis-admin.git
$ cd gitosis-admin

# copy other members' id_rsa.pub files into keydir
$ vim gitosis.conf

# add the following, and save
# <member1>@<server1> and <member2>@<server2> are not the gitosis admin
[group developers]
members = <member1>@<server1> <member2>@<server2>
writable = testproj

[repo testproj]
description = repo for testproj

# copy the <member1>@<server1>.pub and <member2>@<server2>.pub to the keydir/ directory

# commit and push
$ git add gitosis.conf keydir/*
$ git commit -m 'add <member1> and <member2>'
$ git push origin master

# 创建 testproj 工程
$ cd
$ mkdir testproj
$ cd testproj
$ git init

# add anything you want, e.g. test.c

# 提交到远程仓库
$ git add test.c
$ git commit -m 'initial commit for test.c'
$ git remote add origin git@<server-ip>:testproj.git
$ git push origin master

# COMMENT: you don't need to manually add ssh public key to the
# /home/git/.ssh/authorized_keys file, gitosis-admin does this for you!!!
```

## gitweb

gitweb 是基于浏览器的图形化 git 管理工具。

``` bash
sudo apt-get install gitweb
sudo ln -s /user/share/gitweb/ /path/to/htdocs/
sudo vim /etc/gitweb.conf

# 将 $projectroot 改为工程所在目录
# 如工程路径为：/path/to/repositories/xxx.git，则 $projectroot 改为 /path/to/repositories

# 另外，可以修改 xxx.git 目录下的 description 文件，添加一行工程描述
# 还可以新建 cloneurl 文件，添加一行工程的 clone 地址
```

注意：gitweb 默认的页面是 index.cgi，但是 Apache 的 httpd.conf 文件中的 DirectoryIndex 是以 index.html 为起始顺序寻找的，所以我们需要更改一下原来的查找方式，让我们默认就能打开 index.cgi。

``` bash
# 在 gitweb 目录下添加 index.html
<meta http-equiv="refresh" content="0; url=index.cgi">
```

## 让 git status 支持中文字符

如果使用 UTF-8 字符集

``` bash
git config --global core.quotepath false
```

如果使用 GBK 字符集

``` bash
git config --global i18n.logOutputEncoding gbk
git config --global i18n.commitEncoding gbk
```
## github

如果想要在 github 上用 ssh 进行免密码提交，在设置完 ssh key 之后，进行如下测试：

``` bash
$ ssh -vT git@github.com
```

如果检测没有问题，下面假设现在有一个工程，叫 test，那么需要添加 remote 才能正常提交，但是这个 remote 的写法有点特别：

``` bash
$ git remote add origin git@github.com:<username>/test.git
```
