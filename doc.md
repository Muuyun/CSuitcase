## 下载工具
### axel
```shell
axel -n 8 -a https://www.xmind.net/xmind/downloads/XMind-ZEN-for-Linux-64bit.deb
```
> -n 指定线程个数 -a 格式化人性输出

## 包管理器
### dpkg 
```shell
dpkg -i XMind-ZEN-for-Linux-64bit.deb
```
> -i 指定要安装的包


## 常用命令 
### alias 别名
```shell
alias ls='ls -l'
```
> 使用格式 alias 别名='要执行的命令'
> 用来给程序起别名用的
### pkill
```shell
pkill processName
```
> 结束指定进程



## 实用工具
### tldr (和man差不多的东西)
用来获取命令最简单直接的使用方式，不用像man那样繁杂
```shell
# 使用方式
tldr 目标命令
```

## 进程管理
### 把进程放到后台运行

```shell
# Shell退出后也会退出
program &

# Shell退出后还会继续运行
setsid program
nohup program &
(program &)
```
大致原理：在后面加`&`是把进程设置为后台进程, 默认情况下进程是前台进程. 这样会把Shell给占据掉, 就做不了其他的操作了. 

但是这样的话当Shell退出时, 子进程会收到**HUP**信号后退出, 下面就有几种方法解决

1. setsid 将新启动的程序的父进程设置成`init`进程, 这样的话新启动的程序也不会接受到shell的**HUP**信号了

2. nohup 将忽略掉**HUP**信号, 程序也不会退出了。(当Shell退出时, 程序会被`init`进程托管)
3. 将命令放入 **()** 中, 这样命令会在subshell中运行, 这样原理和setsid差不多, 也是父进程被换成了`init`进程



其他情况: 当我们没有处理就提交了命令时要怎么做呢?

Crtl+z 将进程挂起(Suspend), 使用`jobs`命令查看作业号, `bg %作业号`把进程放到后台运行, 不然进程会一直处于挂起状态, 与之相反的命令还有`fg`命令, 这个命令用来把进程放到前台运行。

我们的进程现在就在后台运行了, 但我们还想在shell退出后让进程继续运行要怎么做呢? 我们可以使用`disown %作业号 `命令, 让作业忽略**HUP**信号



> 参考链接 https://www.ibm.com/developerworks/cn/linux/l-cn-nohup
