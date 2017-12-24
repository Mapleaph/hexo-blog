title: Windows Embedded
date: 2015-11-22 11:23:28
categories: Techniques
---

## 自定义 Shell

默认情况下，构建 XPE 时，我们都选择 Explorer Shell，可如果我们想要在进入系统后，默认打开其他的应用程序呢？这里可以使用Component Designer 来建立自定义 Shell 组件，之后在检查依赖性的时候，就可以只选择我们自定义的 Shell 而不选择 Explorer Shell 了。

## 最小化 XPE

在最小化 XPE 的过程中，类似 Networking 这样的组件，虽然我们用不到，可是也需要添加到系统中，否则无法通过依赖性检查。

## EWF (Enhanced Write Filter)

### 介绍

EWF 是 Windows 提供的一种针对磁盘分区的保护机制，将所有对分区的更改都只保留在内存中，并不向磁盘进行任何的数据写入，在系统重启之后，分区能够保持未更改的状态，我们可以用这种方法来防病毒。

### XPE 下 EWF 的安装

1. 在 Target Designer 中添加一下四个组件：

    * EWF NTLDR

    * Enhanced Write Filter

    * Enhanced Write Filter API (EWF API)

    * EWF Manager Console application

2. 需要删除一个组件：

    * NT Loader

3. 在 Enhanced Write Filter 组件的 Settings 中，做如下设定：

    1. 取消 Start EWF Enabled 勾选

    2. 在 Partition Number 一项中填写合适的值，默认是 1，表示保护第一个分区（即 C 盘），用户可以根据实际情况进行选择

    3. Overlay Type 一项，选择 RAM

4. 检查组件依赖性并编译系统镜像

### 使用

FBA 之后的第一次启动，如果在命令行中用 ewfmgr <n>: 来查看现在的 EWF 保护情况，会出现错误，需要将下面的代码制作成 .reg 注册表文件，双击运行并重启系统，之后再用命令 ewfmgr <n>: 查看 EWF 的保护状态，就会给出正常的结果了，当前的状态应该是 Enable。

```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\ewf]
"ErrorControl"=dword:00000001
"Group"="System Bus Extender"
"Start"=dword:00000000
"Type"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\{71A27CDD-812A-11D0-BEC7-08002BE2092F}]
"UpperFilters"="Ewf"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\ewf\Parameters]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\ewf\Parameters\Protected]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\ewf\Parameters\Protected\Volume0]
"VolumeID"="{1EA414D1-6760-4625-8CBE-4F9F85A48E15}"
"Type"=dword:00000001
"ArcName"="multi(0)disk(0)rdisk(0)partition(1)"
```

注意：**ArcName 最后的 partition(n) 应该和 Enhanced Write Filter 中 Partition Number 的值保持一致。**

采用上述系统 Ghost 出来的新系统，需要正常启动两次，才能使用 EWF 功能。

注：Ghost 软件应使用 DOS 下的纯净 Ghost，Laomaotao 下的 Ghost 工具会在镜像里面添加广告文件。

#### 常用命令

```
1. 查看状态
ewfmgr <n>:

2. 启用保护，重启后生效
ewfmgr <n>: -enable

3. 撤销保护，立即生效
ewfmgr <n>: -commitanddisable -live
```

### HORM (Hibernate Once Resume Many)

HORM 是 EWF 当中的一项技术，在系统首次启动后，如果进入休眠状态，之后的关机，无论是正常关机还是异常关机，都会直接从休眠状态中恢复回来，这样可以显著缩短系统启动的时间。