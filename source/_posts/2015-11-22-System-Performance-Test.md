title: 跨平台计算机性能测试套件
date: 2015-11-22 10:52:13
categories: Techniques
---

## CPU 性能测试 [UPDATE]

Stress + htop

## 简介

Phoronix Test Suite 是一款跨平台（Linux/Mac/Windows）的计算机性能测试软件，测试种类共分为五大类：

* System

* Memory

* Processor

* Graphics

* Disk

以上每一个分类里面都有若干个工具，我们是用某个分类里的某个工具来对相关性能进行测试的，下面列出每个分类里面的工具：

### System

hint, idle, juliagpu, mandelbulbgpu, mandelgpu, nexuiz-iqc, pgbench, pybench, smallpt-gpu, sunflow, xplane9-iqc

### Memory

Stream

### Processor

blake2, bork, botan, build-apache, build-php, bullet, byte, c-ray, clomp, compress-7zip, compress-gzip, dcraw, ffmpeg, fhourstones, graphics-magick, himeno, java-scimark2, jgfxbat, john-the-ripper, mafft, mencoder, minion, mrbayes, openssl, polybench-c, primesieve, sample-program, scimark2, stockfish, sudokut, tachyon, tscp

### Graphics

apitest, corebench, j2dbench, nexuiz, openarena, qvdpautest, unvanquished, urbanterror, xonotic, xplane9

### Disk

blogbench, compilebench, postmark, tiobench, unpack-linux

注：这个列表不是很全，没有列出的就是不能用的。

## 安装

将下载好的安装包，现在直接从 phoronix-test-suite-packages 里面提取即可，放到 /path/to/phoronix-dir/download-cache 目录下，然后运行下面的命令进行安装，

``` bash
$ phoronix-test-suite install pts/<tool-name>
```

## 使用

``` bash
$ phoronix-test-suite run <tool-name>
```

## 备份

如果手头没有下载好的 package 安装包，执行安装命令就会直接从网络上面下载工具并安装，之后，如果我们想要把下载好的包提取出来做个备份，只需要执行下面的命令即可，

``` bash
$ phoronix-test-suite make-download-cache
```

命令成功执行后，我们就可以从 /path/to/phoronix-dir/download-cache 目录中找到了。

以上就是基本的介绍和使用指南，hope you enjoy it～
