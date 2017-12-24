title: Things Need to Know about Mac OS X
date: 2015-11-26 21:10:55
categories: Techniques
---

## 快捷键

1. 剪切

    **Command + drag**

2. 复制
    
    **Option + drag**

3. 创建符号链接

    **Command + Option + drag**

## 安装 gdb

Mac OS X 从 10.9 开始选择 lldb 而不是 gdb 作为程序调试器，选用 clang 和 llvm 分别代替 gcc 和 ld。

如果想要安装 gdb，可以通过如下命令实现：

``` bash
brew install homebrew/dupes/gdb
```

## system_profiler (同 lsusb 命令)

``` bash
system_profiler SPUSBDataType
```

## SSH

打开终端使用 SSH 成功连接服务器后，出现

```
warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
```

的问题，解决方法如下：

将 /etc/ssh\_config 文件的

``` bash
SendEnv LANG LC_*
```

一行注释掉，然后保存并重启终端即可。

## 如何在 Mac 下关闭指定端口对应进程

``` bash
lsof -i:<port>
#上面的命令会列出占用端口的进程，可能不止一个，然后根据进程号 kill 掉对应进程
kill <pid>
```

## Doxygen

1. brew install graphviz

2. change the configuration LATEX\_CMD\_NAME to pdflatex

3. change the configuration DOT_PATH to /path/to/dot

4. in the Diagrams Topics of the Wizard tab, change the Diagrams to generate to Use dot tool from the GraphViz package and check all the boxes

5. in the Mode Topics of the Wizard tab, change the Select the desired extraction mode to All Entities and check the Include cross-referenced source code in the output

## 命令行下用 Sublime Text 打开文件

该方法同样适用于其他类似软件，如 MacDown。

* 方法一

    ``` bash
    # 其中 Sublime\ Text 是应用程序的名称
    $ open -a Sublime\ Text <file>
    ```

* 方法二

    ``` bash
    # 先建立软链接
    $ sudo ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl
    # 此后直接用命令打开，更加方便
    $ subl <file>
    ```
