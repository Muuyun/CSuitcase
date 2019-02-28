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

### 容器

### docker(todo)

```shell

```





## 用户管理(todo)





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



## Git 

### git reset

```shell
# 只移动HEAD分支的指向
git reset --soft 指定commit
# 不仅移动了HEAD分支的指向还更新了暂存区, --mixed为默认参数
git reset --mixed 指定commit
# 更新了HEAD分支的指向、暂存区、工作区
git reset --hard 指定commit
# 带路径的reset, 不会移动HEAD分支,只从HEAD分支中复制file.txt到暂存区中, 不能指定--soft
# --mixed 为默认参数。默认指定HEAD分支, 这样可以取消暂存区的修改, 也可以指定别的
git reset --mixed file.txt
# 比--mixed 多更新了工作区
git reset --hard file.txt

# 可以利用reset --soft 移动HEAD的功能作出更灵活的运用比如压缩提交
# 先把HEAD往后移动两个commit， 利用此时暂存区和工作区还是之前的样子, 然后再commit一次就可以实现压缩两个提交了
git reset --soft HEAD~2
git commit -m "压缩前两个提交"
```



### git commit 

```shell
# 修改上一次提交的信息
# 小技巧 : 如果你commit之后又想再提交一些别的修改但又不想再搞多一次commit, 你可以先git add 你要提交的再执行下面的命令, 效果相当于将两次commit合并在一起还能修改提交信息, 但是如果你push了的话就最好不要这样子做了.
git commit --amend 
```

### git grep

```shell
# 在当前工作目录下查找指定字符串或者正则表达式
#  参数: -n 输出行号 --count 输出匹配个数
git grep 12312
```

### git stash

```shell
# 有时，当你在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态，而这时你想要切换到另一个分支做一点别的事情。 问题是，你不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。 针对这个问题的答案是 git stash 命令。
# 储藏会处理工作目录的脏的状态 - 即，修改的跟踪文件与暂存改动 - 然后将未完成的修改保存到一个栈上，而你可以在任何时候重新应用这些改动。
# 默认为 git stash save, 储存修改
git stash 
# 查看储存
git stash list
# 使用储存, 如果不指定储存则使用最近的储存
git stash apply 指定储存
# 丢弃储存
git stash drop 指定储存
# 应用储存并丢弃它
git stash pop 指定储存
```



> 参考阅读 https://git-scm.com/book/zh/v2