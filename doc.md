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



### hexyl (文件十六进制查看工具)

```shell
# 使用方式
hexyl file
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

参考链接 https://www.ibm.com/developerworks/cn/linux/l-cn-nohup



## 内网渗透

### arpspoof (arp欺骗)

```shell
# 若没有此工具可以请尝试安装 dsniff 包
arpspoof -i 指定网卡 -t 欺骗目标 host -r
```

`host`参数 要伪装成的主机(一般为网关或者目标主机)

`-r` 双向欺骗, 使用时要先开启路由转发功能, 可以使用 `echo 1 >> /proc/sys/net/ipv4/ip_forward` 命令开启(不加 `-r` 为单向欺骗, 可以利用来做断网攻击, 这样的话就不要开启路由转发功能了)

**提示**: 可以配合 `driftnet` 工具使用(`dirftnet` 是一个嗅探工具可以嗅探到网络中的图片), 这样就可以观察到目标主机正在游览的图片, 当然你也可以使用别的嗅探工具去获取一些别的信息。

参考链接 https://wizardforcel.gitbooks.io/daxueba-kali-linux-tutorial/content/58.html

### 修改MAC地址

可以使用 `iproute2` 和 `macchanger` 这两个工具包修改 MAC 地址
#### iproute2

```shell
# 查看当前网卡mac
ip link show interface
# 修改前需要先禁用网卡
ip link set dev interface down
# 修改网卡mac地址
ip link set  dev interface address XX:XX:XX:XX:XX:XX
# 重新启用网卡
ip link set dev iinterface up
```
**注意** : MAC 地址每个字节必须为16进制. MAC 地址的后三个字节可以随便设置, 但前三个字节随便设置的话, 有的网络运营商可能拒绝为不正确的 MAC 分配 IP 地址

#### macchanger
```shell
# MAC 地址完全随机
macchanger -r  interface
# MAC 地址随机后三位字节
macchanger -e interface
# 修改成指定 MAC 地址
macchanger --mac=XX:XX:XX:XX:XX:XX
# 恢复成原来的 MAC 地址
macchanger  -p interface
```

//todolist

##无线渗透

###解除认证攻击

```

```



