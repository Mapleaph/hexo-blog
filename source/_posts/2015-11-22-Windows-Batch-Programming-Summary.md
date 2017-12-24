title: 批处理小结
date: 2015-11-22 09:42:09
categories: Techniques
---

## 基本

1. 第一行 @echo off 可以禁止输出命令本身
2. copy 1.txt c:\ >nul 可以禁止输出结果
3. 用双冒号 :: 注释
4. shutdown -a 用于取消强制的关机和重启命令
5. ping -n x 127.1>nul 用于延时 x 秒
6. taskkill /f /im xxx.exe 用于强制关闭当前所有名称为 xxx.exe 的进程

## 执行 xxx.bat 时不显示窗口的方法

新建 vbs 文件，输入以下内容：

```
Set WshShell = CreateObject("WScript.Shell")
WshShell.Run chr(34) & "xxx.bat" & Chr(34), 0
Set WshShell = Nothing
```

## 按任意键才能关闭窗口的方法

```
echo Press Any Key to Continue
pause>nul
exit
```

## 批处理文件执行出现乱码的解决方法

如果文件中包含中文，则一定要将文件用记事本按 ANSI 格式保存，如果按 UTF-8 进行保存，在执行文件的时候原本中文就会显示为乱码。

## 等待进程启动

下面的代码用来检查某个名为 xxx.exe 的进程是否存在，如果不存在则等待到存在为止。

```
:loop
tasklist /FI "IMAGENAME eq xxx.exe" 2>nul | find /I /N "xxx.exe">nul
if %ERRORLEVEL% == 1 goto loop
```

## 读取写入文件

假定有文件 test.txt 其内容为 1，我们将其改为 2，然后保存。

```
set local EnableDelayedExpension
set /p old=< test.txt
set /a new = %old% + 1
echo %new% > test.txt
```
