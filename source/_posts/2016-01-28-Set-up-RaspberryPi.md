title: Set up RaspberryPi
date: 2016-01-28 13:25:25
Categories: Techniques
---

## Installation

最新的系统镜像可以从官网上取得，将解压后得到的 img 文件写入到 TF 卡中。

### dd 命令

1. Mac 下出现了写入不结束的情况，失败。
2. Linux 下写入正常，但写入后的 TF 卡无法挂载，提示重新格式化。

### Win32DiskImager

目前仅能够通过这种方法进行写入。使用过程很简单，这里不做描述。

## Boot Configuration

目前采用的环境是利用 HDMI 转 DVI 线缆链接树梅派和显示器，但默认情况下显示器是没有输出的，需要我们先利用读卡器将 TF 卡挂载到其他系统中，然后编辑 /config.txt 文件，需要去掉几句话的注释，如下：

``` bash
disable_overscan=1
hdmi_force_hotplug=1
hdmi_group=1
hdmi_mode=1
hdmi_driver=2
config_hdmi_boost=4
```
如果你发现现实分辨率不对，那么需要更改其中的两项：

``` bash
hdmi_group=2
hdmi_mode=x
```

其中 x 的值为下表中的编号：

``` bash
hdmi_mode=1 640x350 85Hz
hdmi_mode=2 640x400 85Hz
hdmi_mode=3 720x400 85Hz
hdmi_mode=4 640x480 60Hz
hdmi_mode=5 640x480 72Hz
hdmi_mode=6 640x480 75Hz
hdmi_mode=7 640x480 85Hz
hdmi_mode=8 800x600 56Hz
hdmi_mode=9 800x600 60Hz
hdmi_mode=10 800x600 72Hz
hdmi_mode=11 800x600 75Hz
hdmi_mode=12 800x600 85Hz
hdmi_mode=13 800x600 120Hz
hdmi_mode=14 848x480 60Hz
hdmi_mode=15 1024x768 43Hz DO NOT USE
hdmi_mode=16 1024x768 60Hz
hdmi_mode=17 1024x768 70Hz
hdmi_mode=18 1024x768 75Hz
hdmi_mode=19 1024x768 85Hz
hdmi_mode=20 1024x768 120Hz
hdmi_mode=21 1152x864 75Hz
hdmi_mode=22 1280x768 reduced blanking
hdmi_mode=23 1280x768 60Hz
hdmi_mode=24 1280x768 75Hz
hdmi_mode=25 1280x768 85Hz
hdmi_mode=26 1280x768 120Hz reduced blanking
hdmi_mode=27 1280x800 reduced blanking
hdmi_mode=28 1280x800 60Hz
hdmi_mode=29 1280x800 75Hz
hdmi_mode=30 1280x800 85Hz
hdmi_mode=31 1280x800 120Hz reduced blanking
hdmi_mode=32 1280x960 60Hz
hdmi_mode=33 1280x960 85Hz
hdmi_mode=34 1280x960 120Hz reduced blanking
hdmi_mode=35 1280x1024 60Hz
hdmi_mode=36 1280x1024 75Hz
hdmi_mode=37 1280x1024 85Hz
hdmi_mode=38 1280x1024 120Hz reduced blanking
hdmi_mode=39 1360x768 60Hz
hdmi_mode=40 1360x768 120Hz reduced blanking
hdmi_mode=41 1400x1050 reduced blanking
hdmi_mode=42 1400x1050 60Hz
hdmi_mode=43 1400x1050 75Hz
hdmi_mode=44 1400x1050 85Hz
hdmi_mode=45 1400x1050 120Hz reduced blanking
hdmi_mode=46 1440x900 reduced blanking
hdmi_mode=47 1440x900 60Hz
hdmi_mode=48 1440x900 75Hz
hdmi_mode=49 1440x900 85Hz
hdmi_mode=50 1440x900 120Hz reduced blanking
hdmi_mode=51 1600x1200 60Hz
hdmi_mode=52 1600x1200 65Hz
hdmi_mode=53 1600x1200 70Hz
hdmi_mode=54 1600x1200 75Hz
hdmi_mode=55 1600x1200 85Hz
hdmi_mode=56 1600x1200 120Hz reduced blanking
hdmi_mode=57 1680x1050 reduced blanking
hdmi_mode=58 1680x1050 60Hz
hdmi_mode=59 1680x1050 75Hz
hdmi_mode=60 1680x1050 85Hz
hdmi_mode=61 1680x1050 120Hz reduced blanking
hdmi_mode=62 1792x1344 60Hz
hdmi_mode=63 1792x1344 75Hz
hdmi_mode=64 1792x1344 120Hz reduced blanking
hdmi_mode=65 1856x1392 60Hz
hdmi_mode=66 1856x1392 75Hz
hdmi_mode=67 1856x1392 120Hz reduced blanking
hdmi_mode=68 1920x1200 reduced blanking
hdmi_mode=69 1920x1200 60Hz
hdmi_mode=70 1920x1200 75Hz
hdmi_mode=71 1920x1200 85Hz
hdmi_mode=72 1920x1200 120Hz reduced blanking
hdmi_mode=73 1920x1440 60Hz
hdmi_mode=74 1920x1440 75Hz
hdmi_mode=75 1920x1440 120Hz reduced blanking
hdmi_mode=76 2560x1600 reduced blanking
hdmi_mode=77 2560x1600 60Hz
hdmi_mode=78 2560x1600 75Hz
hdmi_mode=79 2560x1600 85Hz
hdmi_mode=80 2560x1600 120Hz reduced blanking
hdmi_mode=81 1366x768 60Hz
hdmi_mode=82 1080p 60Hz
hdmi_mode=83 1600x900 reduced blanking
hdmi_mode=84 2048x1152 reduced blanking
hdmi_mode=85 720p 60Hz
hdmi_mode=86 1366x768 reduced blanking
```

## apt-get Configuration

``` bash
change the line of /etc/apt/sources.list to
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian jessie main contrib non-free rpi

then comment the line of /etc/apt/sources.list.d/raspi.list
deb http://archive.raspberrypi.org/debian jessie main ui
```

## Chinese Support

Otherwise, square characters will be displayed in the web browser.

``` bash
sudo apt-get install ttf-wqy-zenhei scim-pinyin
```

## Choose the Correct Keyboard Layout

Goto Menu -> Preferences -> Raspberry Pi Configuration: Localisation,

Set Keyboard: choose United States, English(US).
Set Locale: en(English), US(USA), UTF-8.

## Expand the Available Space

Otherwise, you cannot use the whole disk space of your TF card.

Open the terminal, type: sudo raspi-config, then select Expand Filesystem.
