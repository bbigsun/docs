## 简单学下 Linux

> 为了方便新手小白学习 k8s，所以简单写个教程，仅介绍 k8s 入门所需要掌握的一些 Linux 知识。

?> 参考资料：https://www.runoob.com/linux/linux-tutorial.html

### 一、Linux 系统介绍

大体上来讲，Linux分为两个生态体系，红帽和 debian。商业版本以 Redhat 为代表，开源社区版本则以 debian 为代表。

**红帽家族**

- redhat ：红帽自家服务器
- centos ：基于红帽重新封装的去掉版权信息的免费版本
- fedora ：基于红帽社区支持的桌面版，里面的包是红帽的实验版本，相对较新

包的管理方式：yum --> rpm

**debian家族**

- debian ：debian自家纯净系统，一般来说Debian作为适合于服务器的操作系统，它比Ubuntu要稳定得多。
- Ubuntu : 在debian系统上封装了许多工具，Ubuntu有着漂亮的用户界面，完善的包管理系统，强大的软件源支持，丰富的技术社区，但这也决定了它最佳的应用领域 是桌面操作系统而非服务器操作系统。
- Kali : Kali Linux 前身是 BackTrack（基于ubuntu），是一个基于 Debian 的 Linux 发行版，包含很多安全和取证方面的相关工具。

包的管理方式: apt --> deb

*PS：小白可以记住这三个系统，工作使用 CentOS（免费），日常使用 Ubuntu（有桌面），特殊用途 Kali（黑客）*

### 二、Linux 系统安装

学习 Linux，首先我们需要一个 Linux 系统。

- 👉 [CentOS 系统安装](linux-install-centos.md)
- 👉 [Ubuntu 系统安装](linux-install-ubuntu.md)

### 三、Linux 目录介绍

```bash
[root@localhost ~]# ls /
bin  boot  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin selinux  srv  sys  tmp  usr  var
```

以下是对这些目录的解释：

| 目录          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| `/bin`        | bin 是 Binaries (二进制文件) 的缩写， 这个目录存放着最经常使用的命令。 |
| `/boot`       | 这里存放的是启动 Linux 时使用的一些核心文件，包括一些连接文件以及镜像文件。 |
| `/dev`        | dev 是 Device(设备) 的缩写，该目录下存放的是 Linux 的外部设备，在 Linux 中访问设备的方式和访问文件的方式是相同的。 |
| `/etc`        | etc 是 Etcetera(等等) 的缩写，这个目录用来存放所有的系统管理所需要的配置文件和子目录。 |
| `/home`       | 用户的主目录，在 Linux 中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。 |
| `/lib`        | lib 是 Library(库) 的缩写这个目录里存放着系统最基本的动态连接共享库，其作用类似于 Windows 里的 DLL 文件。几乎所有的应用程序都需要用到这些共享库。 |
| `/lost+found` | 这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。 |
| `/media`      | linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux 会把识别的设备挂载到这个目录下。 |
| `/mnt`        | 系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在 /mnt/ 上，然后进入该目录就可以查看光驱里的内容了。 |
| `/opt`        | opt 是 optional(可选) 的缩写，这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。 |
| `/proc`       | proc 是 Processes(进程) 的缩写，/proc 是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。 |
| `root`        | 该目录为系统管理员，也称作超级权限者的用户主目录。           |
| `run`         | 是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。如果你的系统上有 /var/run 目录，应该让它指向 run。 |
| `sbin`        | s 就是 Super User 的意思，是 Superuser Binaries (超级用户的二进制文件) 的缩写，这里存放的是系统管理员使用的系统管理程序。 |
| `selinux`     | 这个目录是 Redhat/CentOS 所特有的目录，Selinux 是一个安全机制，类似于 windows 的防火墙，但是这套机制比较复杂，这个目录就是存放selinux相关的文件的。 |
| `srv`         | 该目录存放一些服务启动之后需要提取的数据。                   |
| `sys`         | 这是 Linux2.6 内核的一个很大的变化。该目录下安装了 2.6 内核中新出现的一个文件系统 sysfs 。sysfs 文件系统集成了下面3种文件系统的信息：针对进程信息的 proc 文件系统、针对设备的 devfs 文件系统以及针对伪终端的 devpts 文件系统。该文件系统是内核设备树的一个直观反映。当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统中被创建。 |
| `tmp`         | tmp 是 temporary(临时) 的缩写这个目录是用来存放一些临时文件的。 |
| `usr`         | usr 是 unix shared resources(共享资源) 的缩写，这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于 windows 下的 program files 目录。`/usr/bin`：系统用户使用的应用程序。`/usr/sbin`：超级用户使用的比较高级的管理程序和系统守护程序。`/usr/src：`内核源代码默认的放置目录。 |
| `var`         | var 是 variable(变量) 的缩写，这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。 |

*PS：在 Linux 系统中，有几个目录是比较重要的，平时需要注意不要误删除或者随意更改内部文件。`/etc`： 上边也提到了，这个是系统中的配置文件，如果你更改了该目录下的某个文件可能会导致系统不能启动。`/bin`, `/sbin`,` /usr/bin`, `/usr/sbin`：这是系统预设的执行文件的放置目录，比如 ls 就是在 /bin/ls 目录下的。值得提出的是：/bin, /usr/bin 是给系统用户使用的指令（除root外的通用户），而 /sbin, /usr/sbin 则是给 root 使用的指令。`/var`： 这是一个非常重要的目录，系统上跑了很多程序，那么每个程序都会有相应的日志产生，而这些日志就被记录到这个目录下，具体在 `/var/log` 目录下，另外 mail 的预设放置也是在这里。*



### 四、Linux 常用命令介绍



1、查看当前目录

```bash
[root@localhost ~]# pwd
/root
```

2、查看当前目录下的文件

```bash
[root@localhost ~]# ls
anaconda-ks.cfg
```

3、查看指定目录下的文件

```bash
[root@localhost ~]# ls /usr
bin  etc  games  include  lib  lib64  libexec  local  sbin  share  src  tmp
```

4、切换目录

```bash
[root@localhost ~]# cd /home
[root@localhost home]# pwd 
/home
```

5、创建空白目录

```bash
[root@localhost home]# mkdir firstdir
[root@localhost home]# ls
firstdir
```

6、创建空白文件

```bash
[root@localhost home]# touch firstfile
[root@localhost home]# ls
firstdir  firstfile
```

7、修改目录或文件名称

```bash
[root@localhost home]# mv firstdir seconddir
[root@localhost home]# ls
firstfile  seconddir
```

8、移动目录或文件

```bash
[root@localhost home]# mv firstfile seconddir/
[root@localhost home]# ls
seconddir
[root@localhost home]# ls seconddir/
firstfile
```

9、复制目录或文件

```bash
[root@localhost home]# cp -r seconddir  threedir
[root@localhost home]# ls
seconddir  threedir
[root@localhost home]# cp threedir/firstfile secondfile
[root@localhost home]# ls
seconddir  secondfile  threedir
```

10、删除目录或文件

```bash
[root@localhost home]# rm -f secondfile
[root@localhost home]# ls
seconddir  threedir
[root@localhost home]# rm -rf seconddir
[root@localhost home]# ls
threedir
```

11、添加新用户

```bash
[root@localhost home]# useradd test
[root@localhost home]# ls
test  threedir
[root@localhost home]# id test
uid=1000(test) gid=1000(test) 组=1000(test)
```

12、修改用户密码

我设置的密码为：test，虽然显示无效密码，不用管它，连续输入两次仍然可以生效。

```bash
[root@localhost home]# passwd test
更改用户 test 的密码 。
新的 密码：
无效的密码： 密码少于 8 个字符
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
```

13、切换用户

```bash
[root@localhost home]# su test
[test@localhost home]$ su root
密码：
[root@localhost home]# 
```

14、查看指定的进程

```bash
[root@localhost home]# ps -ef | grep test
root      24760   1561  0 10:23 pts/0    00:00:00 su test
test      24761  24760  0 10:23 pts/0    00:00:00 bash
```

15、杀死进程

```bash
[root@localhost home]# kill -9 24761
已杀死
```

16、删除用户

```bash
[root@localhost home]# userdel test
[root@localhost home]# ls
test  threedir
[root@localhost home]# id test
id: test: no such user
```

17、编辑文件

进入文件编辑页面后，输入 `i`：进入插入模式，输入内容，输入 `ECS` 退出插入模式，输入 `shifit + :` 进入命令行模式，输入指令 `wq` 退出并保存。

```bash
[root@localhost home]# vi hello.sh
# ----------- 分割线（Enter） -------------- #
~
~
~
~
# ----------- 分割线（i） -------------- #
#!/bin/bash

echo "hello linux"
~                                                                                
-- INSERT --
# ----------- 分割线（ecs） -------------- #
#!/bin/bash

echo "hello linux"
~                                                                                
~ 
# ----------- 分割线（shifit + :） -------------- #
#!/bin/bash

echo "hello linux"
~                                                                                
~                                                                               
:wq
```

18、修改文件权限

```bash
[root@localhost home]# chmod +x hello.sh 
```

19、执行脚本文件

```bash
[root@localhost home]# ./hello.sh 
hello linux
[root@localhost home]# bash hello.sh 
hello linux
```

20、安装 vim 编辑器

```bash
[root@localhost home]# yum -y install vim
```




