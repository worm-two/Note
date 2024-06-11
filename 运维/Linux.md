# 基础篇

## 一 入门知识

### 1.1 概述

###### Linux什么

​	是一个基于POSIX和UNIX的多用户、多任务、支持多线程和多CPU的操作系统

###### 如何理解Linux一切皆文件（Everything is a file）

​	Linux系统中的一切东西全都可以通过文件的方式进行访问或者管理。反过来说，任何被挂在系统中的东西，即使它们的本质并不是文件，也会被OS以文件的眼光来呈现。

> 1. Linux所有文件的后缀只是方便用户使用,对系统没有任何作用
> 2. 文件=内容+属性。使用C函数接口对文件的操作只是对其内容进行增删改；而只有使用的chmod、chgrep命令才是对文件的属性操作

###### Linux目录结构

|    目录     |                            说明                             |
| :---------: | :---------------------------------------------------------: |
|   `/bin`    |                     存放经常使用的命令                      |
|    /sbin    |      s表示Super User,存放系统管理员使用的系统管理程序       |
|  ``/home``  |                      普通用户的主目录                       |
|   `/root`   |                     超级管理员的主目录                      |
|    /lib     | 系统开机所需要的最基本的动态连接共享库,类似Windows的DLL文件 |
| /lost+found |      一般情况下为空,当系统非法关机后,就会存放一些文件       |
|  ==/etc==   |           所有的系统管理所需要的配置文件和子目录            |
|  ==/usr==   |              用户的应用程序和文件默认存放位置               |
|    /boot    |                    Linux启动时的核心文件                    |
|    /proc    |   虚拟目录,是系统内存的映射,可以访问这个目录获取系统信息    |
|    /srv     |              存放一些服务启动后需要提取的数据               |
|    /sys     |                                                             |
|    /tmp     |                      存放一些临时文件                       |
|    /dev     |                   所有硬件以文件形式存储                    |
|    /mnt     |                    临时挂载别的文件系统                     |
|  ==/opt==   |                  主机额外安装软件所在目录                   |
|    /var     |               存放不断扩充或经常被修改的文件                |

###### 开源许可证分类

![image-20240413175747416]（./assets/image-20240413175747416.png）

###### Linux命令的本质

==命令本质上是一个有可执行权限（x）的普通文件==,普通文件的执行需要定位到该文件才能够执行,但是为什么**pwd**命令没有用相对路径或绝对路径定位pwd就可以在任意目录去执行呢？那是因为执行任何命令的时候，`都会从path环境变量所记录的搜索路径中按顺序查找有可执行权限的pwd文件，找到了就拼上路径执行该文件`

###### 什么是Linux发行版

GNU（软件）+Linux内核（系统）=Linux发行版

### 1.2 系统

###### 系统开机流程

​	`开机 -> BIOS -> /boot -> init进程 -> 运行级别 -> 运行级别对应的服务`

###### 系统运行级别

| 级别 | 意义                                                         | 简化              |
| ---- | :----------------------------------------------------------- | ----------------- |
| 0    | 这是系统关闭或关机的级别。在运行级别 0 下，系统会停止所有服务，卸载文件系统，然后关闭计算机。 |                   |
| 1    | 也称为单用户模式，通常用于维护或修复系统。在运行级别1下，系统启动为单用户模式，只有 root 用户能够登录，网络服务被禁用。 |                   |
| 2    | 这是多用户模式，但没有 NFS（网络文件系统）支持。在运行级别 2 下，系统会启动多个用户，但不挂载远程文件系统。 |                   |
| 3    | 这是标准多用户模式，它会启动多个用户并启用所有网络服务。     | multi-user.target |
| 4    | 这通常未使用，您可以根据需要将其配置为自定义模式。           |                   |
| 5    | 这通常与运行级别 3 相同，但它会使用图形用户界面（GUI）。在运行级别 5 下，通常会启动 X 服务器，以便用户可以使用图形桌面环境。 | graphical.target  |
| 6    | 这是系统重新启动的级别。在运行级别 6 下，系统会执行重启操作，类似于运行级别 0。 |                   |

###### 服务器注意事项

1. 远程服务器不允许关机，只能重启
2. 重启前应该关闭服务（例如生产服务，次之网络服务）
3. 不要在服务器访问高峰运和地高负载命令（例解，压缩大文件，大量IO操作，如整盘扫描，复制大文件）
4. 远程配置防火墙时不要把自已踢出服务器
5. 指定合理的密码规范并定期更新
6. 合理分配权限

###### Linux注意事项

1. 大小写敏感,命令长小写
2. 所有内容都是以文件形式保存
3. 不靠扩展名（后缀名）区分文件类型
4. 所有存储设备都必须挂载后才能使用，包括硬盘，U盘，光盘

###### 分区推荐方案

1. /：10G
2. /boot：200M
3. Swap：2G
4. /var：1G以上，可变数据放在其中，例如日志文件，分配大小视用途而定，一般现在的大硬盘，建议分配5G
5. /home：最大的剩余空间的一半，用户的家目录所在
6. /usr：最大的剩余空间的一半，最耗用空间的部份。存放软件的地方

###### Linux内核结构

![image-20240526085827878]（./assets/image-20240526085827878.png）



### 1.3 网络

###### VMware提供3种网络设置

* 桥接模式

​	虚拟机直接接连接外部物理网络的模式，主机起到了桥接网络的作用，这种模式下，==虚拟机可以直接访问外部网络，并且对外部网络是可见的==。

* NAT模式

​	虚拟机和主机构建一个专用网络，并通过虚拟机网络地址转换（NAT）设备对IP进行转换，`虚拟机通过共享主机IP可以访问外部网络，但外部网络无法访问虚拟机`。

* 主机模式

​	虚拟机只与主机共享一个专用网络，**与外部网络无法通信**。

###### 修改IP地址后可能遇到的问题

1. 物理机能ping通虚拟机，但是虚拟机ping不通物理机

​	一般都是因为物理机的防火墙问题,把防火墙关闭就行

2. 虚拟机能Ping通物理机,但是虚拟机Ping不通外网

​	一般都是因为DNS的设置有问题

3. 虚拟机Ping www.baidu.com显示域名未知等信息

​	一般查看GATEWAY和DNS设置是否正确

> * 如果以上全部设置完还是不行,需要关闭（禁用）NetworkManager服务
> * 如果检查发现systemctl status network有问题 需要检查ifcfg-ens33

### 1.4 文件

###### 文件类型

* `-`表示文件
* `d`表示目录
* `l`表示链接文件

###### rwx对文件和目录的作用

1. 文件

   * `r`:可以读取查看

   * `w`:可以修改,==但不能删除文件（删除一个文件的前提是对该文件所在目录有写权限）==
   * `x`:可以被系统执行

2. 目录

   * `r`:可以读取,ls查看你目录内容
   * `w`:可以修改,**目录内创建+删除+重命名目录**
   * `x`:==可以进入该目录==

### 1.5 进程

###### 进程和服务的区别

​	计算机中，一个正在执行的程序或命令，被叫做"进程"（process）。启动之后一只存在、常驻内存的进程，一般被称作"服务"（service）。

###### 内置命令与外部命令

​	一部分基础功能的系统命令是直接内嵌在Shell中的，系统加载启动之后会随着Shell一起加载，常驻系统内存中。这部分命令被称为"内置（built-in）命令"；相应的其它命令被称为"外部命令"

###### 进程信息

![image-20231125114722895]（./assets/image-20231125114722895.png）

###### 终端类型

* ==tty1==:图形化终端
* ==pts==:虚拟终端
* `tty2`:本地的字符界面终端

###### 线程状态

* ==R（运行状态）==
* ==S（睡眠状态）==
* ==T（暂停状态）==
* ==Z（僵尸状态）==
* `s（包含子进程）`
* `l（多线程）`
* `+（前台显示）`

###### Linux终端分类

​	按照是否与计算机主机进行物理连接，可以分为物理终端（与计算机主机直接进行物理连接）和伪终端（通过互联网TCP/IP协议连接的输入和输出设备组合就是伪终端,如使用SSH、Telnet连接）

###### Linux典型应用程序的目录结构

| 文件类型                 | 保存目录       | 说明                           |
| ------------------------ | -------------- | ------------------------------ |
| 普通的可执行程序文件     | /usr/bin       | 所有用户可执行                 |
| 服务器程序、管理程序文件 | /usr/sbin      | 只有管理员可执行               |
| 应用程序配置文件         | /etc           | 通过rpm或yum方式安装的应用程序 |
| 日志文件                 | /var/log       | 系统日志                       |
| 应用程序参考文档文件     | /usr/share/doc | 关于应用程序的参考文档等数据   |
| 应用程序手册页文件       | /usr/share/man | 执行文件及配置文件的man手册页  |

###### Linux软件安装方式

* 源码安装

​	即程序软件的源代码（一般也叫Tarball，即将软件的源码以tar打包后再压缩的资源包）；源码包是作者直接将源程序发布在网上，我们直接下载源文件，自己编译成二进制程序使用

* 二进制安装

​	如Red Hat发行版的.rpm包，Debian发行版的.deb包

* 包安装工具（yum,apt）

###### 源码安装步骤

1. 下载源码包并解压到指定目录
2. 预编译 ==./configure==（--prefix==安装路径，来指定安装路径）
3. 编译 `make`（生成可执行二进制文件）
4. 安装make install（复制二进制文件到系统，配置应用环境）

###### curl和wget的区别

* 相同点
    * curl和wget都是命令行工具，用于从Web服务器下载文件或数据
* 不同点
    * 功能不同：curl是一个通用的工具，支持多种网络协议，包括HTTP、FTP、SMTP、POP3等，可以用于上传、下载、发送邮件等多种操作。 wget则主要用于HTTP和FTP协议的下载，不支持其他协议和操作
    * 输出方式不同：curl的输出结果是文件的内容，wget的输出结果是保存在本地的文件
    * 用法不同：curl的命令选项比较复杂，需要精确设置不同的参数才能完成不同的操作。wget的命令选项相对简单，只需要指定URL和输出文件路径即可
    * 支持情况不同：curl通常已经安装在Linux系统中，wget则可能需要用户手动安装

​	curl由于可自定义各种请求参数所以在模拟web请求方面更擅长；wget由于支持ftp和Recursive所以在下载文件方面更擅长

###### 源码包（原代码安装，脚本安装包）

* 优点

1. 开源，可以自行修改源代码
2. 可以自由选择所需功能
3. 源码包需要编译，更加适合安装的系统，稳定性和效率更高
4. 卸载方便,可直接删除安装的位置， 没垃圾

* 缺点

1. 安装步骤较多，尤其安装大型的软件集合时，容易出现拼写等人为错误
2. 编译时间较长，比二进制包安装慢
3. 安装过程报错新手很难解决

###### 二进制包（RPM包，DEB包，编译后的二进制文件）

* 优点

1. 包管理系统简单。安装，升级，查询，卸载几个命令就可以实现
2. 安装速度比源码包快得多

* 缺点

1. 因为是编译后的，不能再看到源代码
2. 功能选译不如源码包灵活
3. 存在依赖性的问题

###### 软件更新注意事项

​	安装软件的原则是用什么装什么，升级软件也要有针对性的升级不要“yum -y update”。因为这样会连linux的内核在内，所有包一并更新，更新内核后需要配置才能使用，不配置是进入不了系统的。remove也一样，因为依赖性的问题，删除软件有可以使其它软件崩溃，尽量不要卸载



### 1.6 Shell

###### Shell是什么

![image-20240413121838582]（./assets/image-20240413121838582.png）

​	Shell是一个C语言编写的脚本语言，它是用户与Linux的桥梁，用户输入命令交给Shell处理，Shell将相应的操作传递给内核（Kernel）,内核把处理的结果输出给用户（`.sh扩展名并不会影响Shell脚本运行，为的就是做到见名知意`）

###### Shell解析器有哪些

* ==bash==（CentOS）
* **sh（bash的软链接）**
* `dash`（Ubuntu）

###### Shell脚本格式

```shell
# 指定解析器
#!/bin/bash

# 命令内容
echo "hello world"
```

###### #!/bin/bash作用是什么

* "#!"是一个约定标记，用来告诉操作系统这个脚本需要用什么解释器来执行
* /bin/bash指明了Shell解释器的具体位置
* **写Shell脚本时，脚本的第一行必须是“#！/bin/bash”，如果想用别的解释器需要在这里就指定别的解释器的位置，但是不能不写**

###### 脚本执行方式

* 当前进程中执行
  * source 脚本
  * . 脚本

* 新进程中执行
  * ./脚本
  * /bin/bash 脚本

###### 新进程执行脚本

* /bin/bash 脚本:不需要权限,解释器直接执行
* ./脚本:==需要执行权限==,`且脚本第一行为#!/bin/bash`,告诉系统使用什么解释器执行
* 本质是在当前Shell中打开一个子Shell来执行脚本内容，当脚本内容结束，则子Shell关闭回到父Shell中

###### 当前Shell与新Shell执行脚本的区别

环境变量的继承关系

###### Shell脚本中'',"",``的区别

`""会解析执行（所见非所得）`,==''原样输出（所见即所得）==,**``用于命令替换，用于将命令的结果赋值给变量**

###### 将命令的执行结果赋值给变量

* var=\`command\`
* ==var=$（command）==

###### $变量和${变量}的区别

变量名外面的大括号“{}”是可选的，加不加都行，加大括号的作用是帮助解释器识别变量名的边界（`echo "www.${baidu}.com"`）

###### 如何判断Shell脚本是否在当前进程中执行

在Shell脚本中打印当前进程的PID（`echo "当前进程PID为: [$$]"`）

###### Shell变量命名规则

* 变量名称可以由`字母`、`数字`和`下划线`组成，但是==不能以数字开头==，**环境变量名建议大写**
* 等号两侧不能有空格
* **在bash中，变量默认类型都是字符串类型，无法直接进行数值运算**
* 变量的值如果有空格，需要使用双引号或单引号括起来
* 变量名必须以字母或者下划线开头（区分大小写）

###### 常见的系统变量

| 变量名    | 含义                        |
| --------- | --------------------------- |
| $Shell    | 默认Shell                   |
| $HOME     | 当前用户家目录              |
| $IFS      | 内部字段分隔符              |
| $LANG     | 默认语言                    |
| $PATH     | 默认可执行程序路径          |
| $PWD      | 当前目录                    |
| $UID      | 当前用户ID                  |
| $USER     | 当前用户                    |
| $RANDOM   | 随机生成一个0至32767 的整数 |
| $HOSTNAME | 主机名                      |
| $$        | 进程ID                      |

```shell
# 查看所有系统变量
set

# 加载系统变量
source /etc/init.d/functions
```



## 二 核心功能

### 2.1 镜像安装

###### [CentOS](https://juejin.cn/post/7245483735218044983)

###### [Ubuntu](https://blog.csdn.net/abilix_tony/article/details/131339859)

###### Rocky

### 2.2 初始化配置

#### 静态网络配置

1. 查看宿主机网络配置信息

```shell
# 获取IP 网关 DNS 子网掩码
ipconfig /all
```

2. 查看虚拟机网络配置

```shell
ifconfig
```

###### CentOS

3. 进入==/etc/sysconfig/network-scripts==目录

```shel
cd /etc/sysconfig/network-scripts
```

4. 备份配置文件

```shell
cp ifcfg-ens33 ifcfg-ens33.bak
```

5. 修改ifcfg-ens33

```shell
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="0a0803fd-b699-4fec-b439-68d654205ab9"
DEVICE="ens33"
ONBOOT="yes"
# IP地址
IPADDR=192.168.2.38
# 子网掩码
NETMASK=255.255.255.0
# 网关
GATEWAY=192.168.2.1
# DNS
DNS1=192.168.2.1
```

6. 重启网络服务

```shell
systemctl restart network
```

###### Ubuntu

3. 进入==/etc/netplan==

```shell
cd /etc/netplan
```

4. 备份配置文件

```shell
sudo cp 01-network-manager-all.yaml 01-network-manager-all.yaml.bak
```

5. 修改文件权限

```shell
sudo chmod 777 01-network-manager-all.yaml
```

6. 修改网络配置

```yaml
# 根据第一步查询的信息配置
network:
  renderer: networkd
  ethernets:
    ens33: # 一 网卡
    	addresses:
      	  - 192.168.25.128/24 # 二 IP地址
    	nameservers:
      	  addresses: [192.168.25.2] # 三 DNS
    	routes:
      	  - to: default
        	via: 192.168.25.2 # 四 网关
  version: 2
```

7. 重启网络配置服务

```shell
sudo netplan apply
```

###### Rocky

1. 进入网络配置文件目录

```shell
cd /etc/NetworkManager/system-connections
```

2. 修改配置文件

```shell
[connection]
id=ens160
uuid=755751e4-d672-3c2b-a3a6-05860d6a49b6
type=ethernet
autoconnect-priority=-999
interface-name=ens160
timestamp=1715512542

[ethernet]

[ipv4]
method=manual
address1=192.168.2.121/24,192.168.2.1
dns=192.168.2.1

[ipv6]
addr-gen-mode=eui64
method=auto

[proxy]
```



#### 修改下载源

###### CentOS

1. `进入/etc/yum.repos.d目录`

```shell
cd /etc/yum.repos.d/
```

2. ==重命名==

```shell
mv CentOS-Base.repo CentOS-Base.repo.Back
```

3. **下载阿里云的repo文件**

```shell
wget http://mirrors.aliyun.com/repo/Centos-7.repo
```

4. 修改名称

```shell
mv Centos-7.repo CentOS-Base.repo
```

5. 执行yum源跟新mingl

```shell
yum clean all
yum makecache
```

6. 查看yum源

```shell
yum repolist all
```

###### Ubuntu

1. `进入/etc/apt/目录`

```shell
cd /etc/apt/
```

2. ==备份==

```shell
cp sources.list sources.list.bak
```

3. **编辑sources.list**

```shell
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

4. 更新下载源

```shell
apt update
```

5. 更新软件

```shell
# 修复损坏的软件包
apt install -f

# 更新软件
apt upgrade
```

###### Rocky

1. 备份配置文件

```shell
cp -rf ./* ./bak
```

2. 修改文件

```shel
sed -e 's|^mirrorlist=|#mirrorlist=|g' 
\ -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.aliyun.com/rockylinux|g' \ -i.bak 
\ /etc/yum.repos.d/Rocky-*.repo
```

3. 生成缓存

```shell
dnf makecache
```

#### 安装必备工具

```shell
dnf -y install net-tools tree wget lrzsz vim unzip bash-completion build-essential yum-utils telnet telnet-server device-mapper-persistent-data lvm2 nfs-utils gcc gcc-c++ make cmake libxml2-devel curl curl-devel ntp libaio-devel ncurses-devel autoconf automake zlib-devel epel-release openssh-server socat conntrack ntpdate ipvsadm sysstat iostat zip lsof zlib openssl* pcre pcre-devel
```

#### 关闭防火墙

```shell
systemctl stop firewalld && systemctl disable firewalld
```

#### 关闭SElinux

```shell
# 备份
cp /etc/selinux/config /etc/selinux/config.bak

# SELINUX=enforcing 改为 SELINUX=disabled
```

#### 配置主机名和hosts

```shell
# 主机名
vim /etc/hostname

# hosts
vim /etc/hosts
```

#### 时钟同步

```shell
# 时间同步
yum install ntpdate -y
 
ntpdate cn.pool.ntp.org
 
crontab -e * */1 * * * /usr/sbin/ntpdate cn.pool.ntp.org

# 时区
sudo timedatectl set-timezone Asia/Shanghai
```

#### 配置普通用户

```shell
# 添加普通用户
useradd worker

# 将普通用户添加到sudo组
usermod -aG wheel worker

# 禁止root用户SSH登录
sudo sed -i 's/#PermitRootLogin yes/PermitRootLogin no/g' /etc/ssh/sshd_config 
sudo systemctl restart sshd
```

#### 清除邮箱提示信息

```shell
# 清除 "您在 /var/spool/mail/root 中有新邮件"信息
echo "unset MAILCHECK" >> /etc/profile
source /etc/profile
 
# 清空邮箱数据站空间
cat /dev/null > /var/spool/mail/root
```

#### 内核更新

```shell
dnf update
```

#### 内核参数设置

### 2.3 定时任务

#### 语法

> ==*== `*` ***** * * 任务
>
> 分钟 小时 天 月 星期 任务

```shell
# 编辑
crontab -e

# 查询
crontab -l

# 删除
crontab -r

# 重启定时任务服务
systemctl restart crond
```

#### 时间

| 序号    | 含义                 | 范围                    |
| ------- | -------------------- | ----------------------- |
| 第一个* | `一小时中的第几分钟` | 0-59                    |
| 第二个* | `一天中的第几个小时` | 0-23                    |
| 第三个* | `一月中的第几天`     | 1-31                    |
| 第四个* | `一年中的第几个月`   | 1-12                    |
| 第五个* | `一周中的星期几`     | 0-7（0和7都代表星期天） |

| 特殊符号 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| ==*==    | 代表任何时间。比如第一个"*"就代表一小时中每分钟 都执行一次的意思。 |
| ==,==    | 代表不连续的时间。比如"0 8,12,16 * * * 命令"，就代表在每天的8点0分，12点0分，16点0分都执行一次命令 |
| ==-==    | 代表连续的时间范围。比如"0 5 * * 1-6 命令"，代表在周一到周六的凌晨5点0分执行命令 |
| ==*/n==  | 代表每隔多久执行一次。比如"*/10 * * * * 命令"，代表每隔10分钟就执行一遍命令 |

### 2.4 后台任务

### 2.5 软件管理

* yum安装
* 编译安装

#### Linux工具

* wget

```shell
yum -y install wget
```

#### CentOS

###### rpm

> * 是什么
>
>     RPM（RedHat Package Manager），RedHat软件包管理工具，类似windows里面的setup.exe 是Linux这系列操作系统里面的打包安装工具.
>
> * 格式
>
>     * Apache-1.3.23-11.i386.rpm
>         * `apache`是软件名称
>         * `1.3.23-11`是软件的版本号，主版本和此版本
>         * `i386`是软件所运行的硬件平台，Intel 32位处理器的统称
>         * `rpm`文件扩展名，代表RPM包

```shell
# 查询指定安装包
rpm -qa | grep mysql

# 安装软件包
rpm -ivh mysql 

# 升级软件包
rpm -Uvh mysql

# 安装软件包不检查依赖
rpm -ivh --nodeps mysql

# 卸载软件包
rpm -e mysql

# 卸载软件包时不检查依赖
rpm -e --nodeps mysql
```

###### yum

> ​	YUM（全称为 Yellow dog Updater, Modified）是一个在 Fedora 和 RedHat 以及 CentOS 中的 Shell 前端软件包管理器。基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包 并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次 次下载、安装，

| 参数         | 功能                        |
| ------------ | --------------------------- |
| install      | 安装rpm软件包               |
| update       | 更新rpm软件包               |
| check-update | 检查是否有可用的更新rpm软件 |
| remove       | 删除指定的rpm软件           |
| list         | 显示软件包信息              |
| clean        | 清理yum过期的缓             |
| deplist      | 显示yum软件包的所有依赖关   |

```shell
# 列出所有仓库
yum repolist all

# 列出仓库中所有软件包
yum list all

# 安装软件
yum -y install firefox

# 升级软件包
yum update firefox

# 移除软件包
yum remove firefox

# 清除所有仓库缓存
yum clean all
```

* 更新脚本

```shell
# 不更新内核、不升级操作系统版本
yum --exclude=kernel* --exclude=centos-release* update
# 更新内核、升级系统版本
yum update

```

### 2.6 OpenSSL





### 2.7 自定义服务

#### 编写服务

1. 进入配置目录

```shell
# CentOS
/usr/lib/systemd/system

# Ubuntu
/lib/systemd/system/
```

2. 编写服务文件

```shell
[Unit]
Description=服务描述
After=服务依赖（再这些服务后启动本服务）
 
[Service]
Type=服务类型
PIDFile=存放PID文件的位置
ExecStart=启动命令
ExecStop=终止命令
ExecReload=重启命令
 
[Install]
WantedBy=服务安装设置
```

> 1. [Unit]中After的值一般是：network.target remote-fs.target nss-lookup.target
> 2. 在脚本中关于服务启动、重启、关闭的指令需要使用绝对路径，否则会出现无法识别的情况
> 3. 开启启动服务:systemctl enable xxx.service

```shell
[Unit]
Description=deploy report backup servce

[Service]
Type=forking
ExecStart=/opt/deploy-report/deploy-report.sh start
#ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/opt/deploy-report/deploy-report.sh stop
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

3. 重新加载

```shell
systemctl daemon-reload
```

#### 常见问题

###### 启动卡死

* 原因

启动的进程是一个web服务,会一直处于listen状态,导致一直卡住

* 解决方案

编写Shell脚本,后台启动

###### 启动失败

* systemctl执行脚本时需要知道脚本的解释器
* 目标目录的执行权限不够

### 2.8 环境变量

###### 图解环境变量

![image-20240601115800510]（./assets/image-20240601115800510.png）

![image-20240601115843624]（./assets/image-20240601115843624.png）

###### /etc/profile.d/xxx.sh

​	/etc/profile.d 目录下的所有xxx.sh文件，都会被/etc/profile执行，所以对于用户自定义的全局环境变量，一般都是在 /etc/profile.d/目录下新建一个.sh 脚本文件，把自己想要定义的变量写入这个脚本中即可。那么系统在启动之后就会自动执行，并且对所有用户都是可见的（==source /etc/profile.d/xxx.sh==）

###### .bash_profile和.bashrc的区别

* ==.bash_profile：当用户登录时，该文件仅仅执行一次==

* `.bashrc：当用户登录以及每次打开新的Shell时，该文件都会被读取`

###### 环境变量加载过程

* 用户登录

![image-20240601115633816]（./assets/image-20240601115633816.png）

* 用户切换

![image-20240601115651175]（./assets/image-20240601115651175.png）

###### 注意事项

1. 配置文件中存在同名环境变量，后面的变量会覆盖前面的变量
2. 最好在.bashrc 中最后一行添加一句 source /etc/profile,否则可能在重启机器时，出现之前配置的环境变量又失效了的问题

### 2.9 输入输出



## 三 Vim使用



### 一般模式

* 编辑行

| 功能 | 语法                                                         |
| :--: | ------------------------------------------------------------ |
| 复制 | yy:复制光标当前行 y数字y:从当前行开始复制几行 y$:复制行首到光标位置 y^:复制光标位置到行尾 |
| 粘贴 | p                                                            |
| 撤销 | u                                                            |
| 删除 | dd:删除当前行 d数字d:从当前行开始删除几行 y$:删除行首到光标位置 y^:删除光标位置到行尾 |

* 编辑字符

| 功能         | 语法                                            |
| ------------ | ----------------------------------------------- |
| 删除单个字符 | x:删除当前光标所在字符 X:删除当前光标前一个字符 |
| 复制一个单词 | yw（**光标需置于单词前**）                      |
| 删除一个单词 | dw（**光标需置于单词前**）                      |

* 移动光标

| 功能                     | 语法              |
| ------------------------ | ----------------- |
| 行首                     | ^                 |
| 行尾                     | $                 |
| 页头                     | 1+G               |
| 页尾                     | G                 |
| 目的行                   | 数字+G（数字+gg） |
| 第一行                   | gg（H）           |
| 屏幕顶行                 | H                 |
| 屏幕低行                 | L                 |
| 移动到下一个单词的单词首 | w                 |
| 移动到上一个单词的单词首 | b                 |
| 移动到下一个单词的单词尾 | e                 |

### 编辑模式

| 功能             | 按键 |
| ---------------- | ---- |
| 当前光标前       | i    |
| 光标所在行最前   | I    |
| 当前光标后       | a    |
| 光标所在行最后   | A    |
| 当前光标的上一行 | O    |
| 当前光标的下一行 | o    |
| 推出编辑模式     | esc  |

### 指令模式

* 保存文件

| 功能     | 指令 |
| -------- | ---- |
| 保存     | :w   |
| 退出     | :q   |
| 强制执行 | :!   |

* 查找

| 功能            | 指令                                      |
| --------------- | ----------------------------------------- |
| 高亮            | :noh（取消高亮显示）                      |
| 行号            | :set nu（设置行号） :set nonu（关闭行号） |
| /要查找的字符串 | n查找下一个,N查找上一个                   |
| ! Linux命令     | 在vim的底行模式下执行Linux的基本命令      |
| gg + dG         | 清空文件                                  |

* 替换

| 功能                       | 语法         |
| -------------------------- | ------------ |
| 替换当前行第一个匹配的内容 | s/old/new    |
| 替换当前行所有匹配的内容   | s/old/new/g  |
| 替换文件每一行匹配的内容   | %s/old/new   |
| 替换文件所有匹配的内容     | %s/old/new/g |

```shell
# 打开某个已存在的文件，从第 6 行开始编写
vim +6 File.cfg

# 打开某个已存在的文件，以只读模式进入
vim -R File.cfg
```











## 四 Shell

### 4.1 变量

#### 变量分类

###### 普通变量

```shell
# 定义变量
message="毕竟几人真得鹿,不知终日梦为鱼"

# 引用便来
echo $message
echo ${message}

# 删除变量
unset message
```

###### 静态变量

```shell
# 静态变量（不能使用unset撤销,也无法被修改）
readonly meg="孤舟蓑笠翁,独钓寒江雪"
```

#### 作用域

###### 局部变量

定义在函数内部,使用==local==关键字修饰，`函数外无法使用`

###### 全局变量

Shell中定义的变量默认是全局变量,可以在在`当前Shell进程和子进程使用`（在当前Shell进程中执行Shell脚本，脚本中的全局变量可以在当前Shell进程中使用）

###### 环境变量

使用==export==导出全局变量，可以在`当前Shell进程和子Shell进程使用`，**环境变量传子不传父**

###### 系统变量

定义在系统配置文件中,可以在`整个系统使用`





###### 方法变量

| 变量名 | 作用                                                         |
| ------ | ------------------------------------------------------------ |
| $n     | n为数字，`$0代表该脚本名称`，**$1-$9代表第一到第九个参数**，==十以上的参数需要用大括号包含==，如${10} |
| $#     | 获取==所有输入参数个数==，常用于循环,判断参数的个数是否正确以及加强脚本的健壮性 |
| $*     | 这个变量代表命令行中所有的参数，`$*把所有的参数看成一个整体` |
| $@     | 这个变量也代表命令行中所有的参数，`$@把每个参数区分对待`     |
| $?     | 最后一次执行的命令的返回状态。==如果这个变量的值为0，证明上一个命令正确执行==；如果这个变量的值为非0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了 |
| $0     | 脚本自身名字                                                 |
| $$     | 当前进程PID                                                  |
| $!     | 上一条运行后台进程的PID                                      |

### 二 表达式







### 三 条件判断

#### 运算符

==$（（运算式））== `$[运算式]`

#### 判断逻辑

* ==test condition==
* `[ condition ]` （**condition前后要有空格**）

> 条件非空即为true,[ test ]返回true,[  ]返回false

#### 常见判断条件

* 数值

| 符号    | 作用     |
| ------- | -------- |
| ==-eq== | 等于     |
| ==-ne== | 不等于   |
| `-lt`   | 小于     |
| `-le`   | 小于等于 |
| **-gt** | 大于     |
| **-ge** | 大于等于 |

* 字符串

| 符号 | 作用   |
| ---- | ------ |
| =    | 等于   |
| !=   | 不等于 |

* 文件权限

| 符号 | 作用     |
| ---- | -------- |
| -r   | 读权限   |
| -w   | 写权限   |
| -x   | 执行权限 |

* 文件类型

| 符号 | 作用           |
| ---- | -------------- |
| -e   | 文件或目录存在 |
| -f   | 文件存在       |
| -d   | 目录存在       |

* 逻辑判断

| 符号     | 作用                                        |
| -------- | ------------------------------------------- |
| ==&&==   | 表示前一条命令执行成功时，才执行后一条命令  |
| ==\|\|== | 表示上一 条命令执行失败后，才执行下一条命令 |

> [ test ] && echo "OK" || echo "NOT OK"

```shell
# 数值
[ 23 -ge 22]

# 文件权限
[ -w test.sh]
```



### if

* 单分支

```shell
if [ ondition ] 
then 
	程序
fi

if [ ondition ] 
then 
	程序1
else
	程序2
fi
```

* 多分枝

```shell
if [ condition ]
then
	程序1
elif [ condition ]
then
	程序2
else
	程序3
fi
```

> 1. [ 条件判断式 ]条件判断的中括号和条件判断式之间必须有空格
> 2. ==if后要空格==

### case

* 语法

```shell
case $变量名 in
"值1"） 
	程序1
;;
"值2"）
	程序2
;;
*）
	上述判断都不成立,执行此程序
;;
esac
```

> 1. ==case行尾必须为单词"in"，每一个模式匹配必须以右括号"）"结束==
> 2. `双分号;;"表示命令序列结束，相当于java中的break`
> 3. `最后的""*）"表示默认模式，相当于java中的default`

### 四 循环控制

### for

* 方式一

```shell
for （（初始值;循环控制条件;变量变化））
do
	程序
done
```

```shell
# 从1加到100
#!/bin/bash

sum=0
for（i=0;i<=100;i++）
do
	sum=$[$sum+$i]
done

echo $sum
```

* 方式er

```shell
for 变量 in 值1 值2 值3 ...
do
	程序
done
```

```shell
# 打印所有输入参数
#!/bin/bash

for i in $@
do
	echo "the value is $i"
done
```

> 1. $*和$@都表示传递给函数或脚本的所有参数，不被双引号""包含时，都以$1 $2 …$n 的形式输出所有参数
> 2. 当它们被双引号""包含时，$*会将所有的参数作为一个整体，以""$1 $2 …$n"的形式输出所有参数；$@会将各个参数分开，以"$1" "$2"…"$n"的形式输出所有参数。

### while

```shell
while [ condition ]
do
	程序
done
```

```shell
# 从1加到100
#!/bin/bash

sum=0
i=1
while [ $i -le 100 ]
do
	sum=$[$sum+$i]
	i=$[$i+i]
done

echo $sum
```

### 五 函数

* 控制台输入

```shell
#!/bin/bash

read -t 7 -p "Enter your name in 7 seconds : " name
echo "your name is $name"
```

> 1. `-p指定读取值时的提示符`
> 2. `-t指定读取值等待的时间（秒）,如果不加-t表示一直等待`
> 3. **最后变量为指定读取值的名称**

### 系统函数

#### basename

```shell
# 获取路径里的文件名称
basename /root/fruit/apple.txt

# 获取路径里的文件名称,不带后缀
basename /root/fruit/apple.txt .txt
```

#### dirname

```shell
# 获取文件路径的绝对路径
dirname /root/fruit/apple.txt
```

### 自定义函数

#### 语法

```shell
[ function ] functionName[（）]
{
	Action;
	[return int;]
}
```

> 1. `必须在调用函数地方之前，先声明函数，Shell脚本是逐行运行。不会像其它语言一样先编译`
> 2. ==函数返回值只能通过$?系统变量获得，可以显示加：return返回，如果不加，将以最后一条命令运行结果，作为返回值。return后跟数值 n（0-255）==

```shell
#!/bin/bash
function sum（）
{
s=0
s=$[$1+$2]
echo "$s"
}

read -p "Please input the number1: " n1;
read -p "Please input the number2: " n2;
sum $n1 $n2;
```

## 五 常见问题













# 中级篇

![Linux 常用命令壁纸]（./assets/Linux 常用命令壁纸.jpg）

### 系统命令

#### CPU

###### cpupower

cpupower 命令的功能是调整 CPU 主频参数。Linux 系统内核支持根据使用场景来调节
CPU 主频参数，以提高计算性能或降低功耗。
对于移动设备来讲，在没有接通电源的时候，续航是很重要的，此时就可以启用 CPU 休
眠功能。而服务器一直接着电源，而且需要具有很强的性能，此时就应禁止 CPU 休眠功能，
并把 CPU 主频固定到最高值

```shell
# 查看当前 CPU 的全部主频信息
cpupower -c all frequency-info

# 设置当前 CPU 为性能模式
cpupower -c all frequency-set -g performance

# 设置当前 CPU 为节能模式
cpupower -c all frequency-set -g powersave


# 查看 CPU 主频信息
cpupower frequency-info


```









#### 数据同步

###### sync

* 将数据由内存同步到硬盘

> ​	Linux 系统中为了提高磁盘的读写效率，对磁盘采取了 "预读迟写"操作方式。当用户保存文件时，Linux 核心并不一定立即将保存数据写入物理磁盘中，而是将数据保存在缓冲区中，等缓冲区满时再写入磁盘，这种方式可以极大的提高磁盘写入数据的效率。但是也带来了安全隐患，如果数据还未写入磁盘时，系统掉电或者其他严重问题出现，则将导致数据丢失。使用sync指令可以立即将缓冲区的数据写入磁盘



#### 关机

###### halt

* 关闭系统,但不断电

###### poweroff

* 关机

###### reboot

* 重启

######  shutdown

* shutdown 命令的功能是关闭服务器的系统，作用等同于 poweroff 命令

```shell
# 计算机将在 1 分钟后关机，并且会显示在登录用户的当前屏幕中
shutdown -h 1 'This server will shutdown after 1 mins'

# 立马关机（等同于poweroff）
shutdown -h now

# 系统立马重启（等同于reboot）
shutdown -r now

# 设定当前服务器在指定时间自动关机，格式为“小时:分钟”
 shutdown -h 21:00
 
# 设定当前服务器在 5 分钟后关机，同时发送警告信息给所有已登录的用户
shutdown +5 "System will shutdown after 5 minutes"

# 取消当前服务器上已有的关机任务
shutdown -c
```

#### 帮助

###### man

* man 命令来自英文单词 manual 的缩写，中文译为“帮助手册”，其功能是查看命令、配置文件及服务的帮助信息。一般而言，网上搜索来的资料普遍不够准确，或者缺乏系统性，导致学习进度缓慢，而 man 命令作为权威的官方工具，很好地解决了上述两点弊病。一份完整的帮助信息包含以下信息

```shell
# 查看指定命令的帮助信息
man ls

# 查看指定配置文件的帮助信息
man 5 passwd

# 找到某个命令的帮助信息的存储位置
 man -w ls
 
# 到某个配置文件的帮助信息的存储位置
man -w 5 passwd
```

​	man命令比较适合用来查看Shell外部命令的帮助文档，内部命令是直接内嵌在Shell中的，系统加载启动 之后会随着Shell一起加载，常驻系统内存中；其他的命令成为外部命令，外部命令有些比较大，用的时候才会去加载执行。

###### help

* 查看Shell内置命令信息
* ==命令 --help==:查看外部命令的帮助信息

help 命令的功能是显示帮助信息，能够输出 Shell 内部命令的帮助内容，但对于外部命令
则无法使用，需要用 man 或 info 命令进行查看

```shell
# 以默认格式显示指定命令的帮助信息
help cd


# 以短格式显示指定命令的帮助信息
help -s cd

# 以简短格式显示指定命令的帮助信息

help -d cd

# 以 man 手册格式显示指定命令的帮助信息
help -m cd







```



###### type

* 查看命令类型

###### which

* which 命令的功能是查找命令文件，能够快速搜索二进制程序所对应的位置。如果我们既不关心同名文件（find 与 locate），也不关心命令所对应的源代码和帮助文件（whereis），仅仅是想找到命令本身所在的路径，那么这个 which 命令就太合适了

```shell
# 查找某个指定命令文件的所在位置
which reboot

# 查找多个指定命令文件的所在位置
which shutdown poweroff
```





#### 清屏

###### clear

* 清屏

###### reset

* 彻底清屏

#### 时间

###### date

* date 命令的功能是显示或设置系统日期与时间信息。运维人员可以根据想要的格式来输出系统时间信息，时间格式为MMDDhhmmCCYY[.ss]，其中 MM 为月份，DD 为日，hh为小时，mm 为分钟，CC 为年份前两位数字，YY 为年份后两位数字，ss 为秒的值

```shell
# 显示当前时间
date

# 显示当前年份 月份 那一天
date +%Y
date +%m
date +%d

# 按照指定格式显示时间
date "+%Y-%m-%d %H:%M:%S"

# 显示指定字符串的时间
date -d "2022-10-10 12:12:30"
# 显示前一天
date -d '1 days ago'
# 显示后一天
date -d '-1 days ago'

# 设置系统时间
date -s "2023-06-19 20:52:10
```

###### cal

```shell
# 显示本月日历
cal

# 显示只从年份日历
cal 2023
```

###### chrony

* chronyc 命令来自英文词组 chrony command-line 的缩写，其功能是设置时间与时间服务器同步。chrony 是一个用于保持系统时间与 NTP 时间服务器同步的服务，其守护进程 chronyd更为常见，而 chronyc 命令则是用户的配置工具

```shell
# 查看当前系统的时间同步源信息
chronyc sources -v
```





#### 信息

###### uname

* uname 命令来自英文词组 UNIX name 的缩写，其功能是查看系统主机名、内核及硬件架构等信息。如果不加任何参数，默认仅显示系统内核名称（相当于-s 参数）的作用

```shell
# 显示系统内核名称
uname

# 显示系统所有相关信息（含内核名称、主机名、版本号及硬件架构）
 uname -a
 
# 显示系统内核版本号
uname -r

# 显示系统硬件架构
uname -i
```

###### startx

* startx 命令来自英文词组 start X-window 的缩写，其功能是启动 X-window 系统。X-window 也被称为 X 或 X11，中文译为 X 窗口系统，用于以图形方式显示软件窗口，现在的 GNOME 和 KDE 桌面环境都是以 X 窗口系统为基础构建成的

```shell
# 以默认方式初始化启动 X 窗口系统
startx

# 指定以 16 位颜色深度启动 X 窗口系统
startx --depth 16

# 强制启动 X 窗口系统
 startx -w
```

###### history

* history 命令的功能是显示与管理历史命令记录。Linux 系统默认会记录用户执行过的有命令，可以使用 history 命令查阅它们，也可以对其记录进行修改和删除操作

```shell
# 显示执行过的全部命令记录
history

# 显示最近执行过的 5 条命令
history 5

# 将本次缓存区信息写入历史文件（~/.bash_history）
history -w

# 将历史文件中的信息读入当前缓冲区
history -r

# 将本次缓冲区信息追加写入历史文件（~/.bash_history）
history -a

# 清空本次缓冲区及历史文件中的信息
history -c
```

###### env

* env 命令来自英文单词 environment 的缩写，其功能是显示和定义环境变量。为了能够让每个用户都拥有独立的工作环境，Linux 系统使用了大量环境变量，可以使用 env 命令进行查看和修改

```shell
# 显示当前系统的全部环境变量信息
env

# 删除当前系统中的指定环境变量
env -u PWD

# 定义当前系统中的指定环境变量值
env PWD=/Dir

```

###### setenv

* setenv 命令来自英文词组 set environment variable 的缩写，其功能是设置与显示系统环境变量信息。设置的环境变量如需永久生效，需要将其写入/etc/profile 或/etc/bashrc 文件

```shell
# 显示当前系统中全部的环境变量信息
setenv

# 设置一个新的系统环境变量信息
setenv WEBSITE linuxcool.com
```

###### journalctl

* journalctl 命令来自英文词组 journal control 的缩写，其功能是查看指定的日志信息。在RHEL 7/CentOS 7 及以后版本的 Linux 系统中，systemd 服务统一管理了所有服务的启动日志，带来的好处就是可以只用 journalctl 一个命令来查看全部的日志信息了

```shell
# 查看系统中全部的日志信息
journalctl

# 指定查看内核日志信息
journalctl -k

# 定查看本次系统启动的日志信息
journalctl -b

# 指定查看某个服务程序的日志信息
journalctl -u sshd

# 指定查看最近 10 条日志信息
journalctl -n 10

# 持续追踪最新的日志信息，保持刷新内容
journalctl -f
```





### 权限命令

#### 用户组

###### groupadd

* groupadd 命令的功能是创建新的用户组。每个用户在创建时都有一个与其同名的基本组，后期可以使用 groupadd 命令创建出新的用户组信息，让多个用户加入指定的扩展组，从而为后续的工作提供了良好的文档共享环境

```shell
# 添加一个新用户组
groupadd gou

# 查看所有用户组
cat /etc/group

# 创建一个新的用户组，并指定用户组 ID
groupadd -g 6688 linuxprobe

# 创建一个新的用户组，设定为系统工作组
groupadd -r linuxprobe
```

###### groupdel

```shell
# 删除一个用户组
groupdel gou
```

###### groupmod

```shell
# 修改用户组名
groupmod -n xu gou
```

#### 用户

###### useradd

* useradd 命令的功能是创建并设置用户信息。使用 useradd 命令可以自动完成用户信息、基本组、家目录等的创建工作，并在创建过程中对用户初始信息进行定制。针对已创建的用户，则需使用 chmod 命令修改用户信息，使用 passwd 命令修改密码信息
* adduser 命令的功能是创建用户。adduser 实际上并不是一个真正的命令，而仅仅是 useradd的一别名命令，因此这两个命令的使用方法完全相同

```shell
# 新增用户
useradd ming

# 新增用户到组
useradd -g gou ming


# 创建指定的用户信息，但不创建家目录，亦不让登录系统
useradd -M -s /sbin/nologin linuxprobe

# 创建指定的用户信息，并自定义 UID 值
useradd -u 6688 linuxprobe

# 创建指定的用户信息，并指定过期时间
useradd -e "2024/01/01" linuxprobe

# 创建指定名称的用户，并设置家目录名称
useradd  -d /home/linux linuxprobe
```

###### passwd

* passwd 命令来自英文单词 password 的缩写，其功能是修改用户的密码值；同时也可以对用户进行锁定等操作，但需要管理员身份才可以执行

```shell
# 修改当前登录用户的密码值
passwd

# 修改指定用户的密码值
passwd ming

# 锁定指定用户的密码值，不允许其进行修改
passwd -l linuxprobe

# 解锁指定用户的密码值，允许其进行修改
passwd -u linuxprobe

# 强制指定的用户在下次登录时必须重置其密码
passwd -e linuxprobe

# 删除指定用户的密码值
passwd -d linuxprobe

# 查看指定用户的密码状态
passwd -S linuxprobe
```

###### userdel

* userdel 命令来自英文词组 user delete 的缩写，其功能是删除用户信息。在 Linux 系统中，一切都是文件，用户信息被保存到了/etc/passwd、/etc/shadow 以及/etc/group 文件中，因此使用 userdel 命令实际上就是删除指定用户在上述 3 个文件中的对应信息

```shell
# 删除用户,保存用户主目录
userdel ming

# 删除用户和主目录
userdel -r ming
```

###### usermod

* usermod 命令来自英文词组 user modify 的缩写，其功能是修改用户信息中的各项参数。在创建用户后如果发现信息错误，可以不用删除，而是用 usermod 命令直接修改用户信息，并且参数会立即生效

```shell
# 修改用户组
usermod -g root ming

# 添加到root用户组
usermod -aG root ming

# 修改指定用户的家目录路径
usermod -d /home linuxprobe

# 修改指定用户的 ID
usermod -u 6688 linuxprobe

# 修改指定用户的名称为 linuxcool
usermod -l linuxcool linuxprobe

# 锁定指定的用户，临时不允许登录系统
usermod -L linuxcool

# 解锁指定的用户，再次允许登录系统
usermod -U linuxcool
```

#### 权限

```shell
#  sudo设置普通用户具有root权限
vim /etc/passwd
ming ALL=（ALL） NOPASSWD:ALL
```

###### chmod

* chmod 命令来自英文词组 change mode 的缩写，其功能是改变文件或目录权限的命令。默认只有文件的所有者和管理员可以设置文件权限，普通用户只能管理自己文件的权限属性。设置权限时可以使用数字法，亦可使用字母表达式，对于目录文件，建议加入-R 参数进行递归操作，这意味着不仅对于目录本身，而且也对目录内的子文件/目录进行新权限的设定。

```shell
# 改变文件权限
# 方式一 chomd {ugoa} {+-=} {rwx} 目录或文件
chmod g+x,a-r apple.txt

# 方式二 chmod {mode-421} 文件或目录
chmod 666 apple.txt

# 修改目录内所有权限
chmod -R 777 fruit/

# 为某个二进制命令文件新增 SUID 特殊权限位
chmod u+s /sbin/reboot
```

###### chown

* chown 命令来自英文词组 change owner 的缩写，其功能是改变文件或目录的用户和用户组信息。管理员可以改变一切文件的所属信息，而普通用户只能改变自己文件的所属信息

```shell
# 改变文件所有者
chown tian apple.txt

# 改变指定文件的所属主与所属组
chown root:root File.txt

# 改变文件所有者和所属组
chown -R ming:gou fruit/
```

###### chgrp

```shell
# 改变文件所属组
chgrp gou pear.txt
```

###### getfacl

* getfacl 命令来自英文词组 get file access control list 的缩写，其功能是显示文件或目录的ACL 策略。对指定的文件或目录进行精准的权限控制，FACL（File Access Control List，文件访问控制列表）一定是不二之选，快用 getfacl 命令看看文件都有哪些权限吧

```shell
# 查看指定文件上有哪些访问控制策略
getfacl File.txt

# 查看指定文件上有哪些访问控制策略，不显示注释信息
getfacl -c File.txt
 
# 使用表格形式查看指定文件上有哪些访问控制策略
getfacl -t File.txt
```

###### getfacl

* setfacl 命令来自英文词组 set file access control list 的缩写，其功能是设置文件 ACL 策略规则。FACL 即文件访问控制列表策略，通过该技术可以更加精准地控制权限的分配，例如仅允许某个用户访问指定目录，或仅有某个用户才具有写入权限。把权限约束在一个极小的范围内，系统也就更加安全了

```shell
# 对目录进行 FACL 策略规则设置，允许指定用户进行读、写、执行操作
setfacl -Rm u:linuxcool:rwx File

# 清除指定目录上已有的 FACL 策略规则
setfacl -x u:linuxcool File
getfacl File


```

###### chattr

* 仅能查看文件的一般权限、特殊权限、SELinux 安全上下文与是否有 FACL（文件访问控制列表）等情况，但却无法查看到文件隐藏属性，所以有些运维人员甚至不清楚竟然还有第五种文件权限

```shell
# 给指定文件添加隐藏属性
chattr +i File.cfg

# 从指定文件移除隐藏属性
chattr -i File.cfg

# 给指定目录添加隐藏属性，递归操作
chattr -R +i /Dir

# 从指定目录移除隐藏属性，递归操作
chattr -R -i /Dir

```





#### 查询

###### id

* id 命令的功能是显示用户与用户组信息。UID 是用户身份的唯一识别号码，相当于我们的身份证号码，而 GID 则是用户组的唯一识别号码。用户仅有一个基本组，但可以有多个扩展组

```
# 查看用户是否存在
id ming

# 查看有哪些用户
cat /etc/passwd

# 显示当前用户的所属群组的 ID（GID）
id -g

#显示当前用户的 ID（UID）
id -u

# 查询当前用户的身份信息
id linuxprobe
```

###### who

```shell
# 显示自身用户名称
whoami

# 显示登录用户的用户名以及登陆时间 
who am i
```

###### su

* su 命令来自英文单词 switch user 的缩写，其功能是切换用户身份。将管理员切换至任意用户身份时无须密码验证，而将普通用户切换至任意用户身份时均需密码验证。另外，添加单个减号（-）参数表示完全的身份变更，不保留之前用户的任何环境变量信息

```shell
# 切换用户获取权限,不能获取环境变量
su root

# 切换用户获取权限,获取该用户的环境变量
su - root
```

###### sudo

* sudo 命令来自英文词组 super user do 的缩写，中文译为“超级用户才能干的事”，其功能是授权普通用户执行管理员命令。使用 su 命令变更用户身份虽然好用，但是需要将管理员的账户密码告诉他人，总感觉心里不踏实，幸好有了 sudo 服务。使用 sudo 服务可以授权某个指定的用户执行某些指定的命令。通过在满足工作需求的前提下尽可能少放权，可保证服务器的安全。配置 sudo 服务时，可以直接编辑配置文件/etc/sudoers，亦可以执行visudo 命令进行设置，一切妥当后普通用户便能够使用 sudo 命令进行操作了

```shell
# 查看当前用户有哪些被 sudo 服务授权的命令
 sudo -l
 
# 使用某个被sudo 服务允许的用户身份来执行管理员的重启命令
 sudo -u linuxprobe "reboot"
 
# 使用当前用户身份，基于 sudo 命令来执行管理员的重启命令
sudo reboot
```



### 正则表达式

#### 特殊字符

| 符号  | 功能                                                      | 实例                    |
| ----- | --------------------------------------------------------- | ----------------------- |
| ==^== | `匹配开头行`                                              | **^a（以a开头）**       |
| ==$== | `匹配结尾行`                                              | **t$（以t结尾）**       |
| ==.== | `匹配任意一个字符`                                        | **r..t（如root）**      |
| ==*== | `不单独使用，和上一个字符连用，表示匹配上一个字符0次或多` | **ro*t（如roooot,rt）** |

#### 字符区间

| 符号 | 功能                   | 实例                                                   |
| ---- | ---------------------- | ------------------------------------------------------ |
| []   | 配置指定范围的一个字符 | [6,8] （匹配6或8）,[0-9] （匹配uige0到9的字符）,[0-9]* |
| \    | 表示转移               | 'a\\$b'（匹配a$b）                                     |

### awk

#### 语法格式

awk 参数 文件名

#### 命令参数

| 参数名 | 作用                   |
| ------ | ---------------------- |
| -f     | 从脚本中读取 awk 命令  |
| -F     | 设置输入时的字段分隔符 |
| -v     | 自定义变量信息         |
| -e     | 指定源码文件           |

#### 内置参数

| 参数名   | 功能                         |
| -------- | ---------------------------- |
| ARGC     | 命令行参数个数               |
| ARGV     | 命令行参数排列               |
| ENVIRON  | 支持在队列中使用系统环境变量 |
| FILENAME | awk 浏览的文件名             |
| FNR      | 浏览文件的记录数             |
| FS       | 设置输入域分隔符             |
| NF       | 浏览记录域的个数             |
| NR       | 已读的记录数                 |
| OFS      | 输出域分隔符                 |
| ORS      | 输出记录分隔符               |
| RS       | 控制记录分隔符               |

#### 示例

```shell
# 仅显示指定文件中第 1、2 列的内容（默认以空格为间隔符）
awk '{print $1,$2}' File.cfg

# 以冒号为间隔符，仅显示指定文件中第1列的内容
awk -F : '{print $1}' /etc/passwd

# 以冒号为间隔符，显示系统中所有 UID 号码大于 500 的用户信息（第 3 列）
awk -F : '$3>=500' /etc/passwd

# 仅显示指定文件中含有指定关键词root的内容
awk '/root/{print}' File.cfg

# 以冒号为间隔符，仅显示指定文件中最后一个字段的内容
awk -F : '{print $NF}'
```











### 文件命令

#### 目录

###### pwd

* pwd 命令来自英文词组 print working directory 的缩写，其功能是显示当前工作目录的路径，即显示所在位置的绝对路径。在实际工作中，我们经常会在不同目录之间进行切换，为了防止“迷路”，可以使用 pwd命令快速查看当前所处的工作目录路径，方便开展后续工作

```shell
# 查看当前工作目录路径
pwd
```



###### ls

* ls命令来自英文单词list的缩写，中文译为“列出”，其功能是显示目录中的文件及其属性信息

```shell
# 列出全部目录,包括隐藏文件
ls -a

# 列出目录详情
ls -l
ll

# 显示指定目录中的文件列表
ls /etc

# 显示当前目录中的文件名及 inode 属性信息
ls -i

# 结合通配符一起使用,显示指定目录中所有以sd开头的文件列表
ls /dev/sd*

# 依据文件内容大小进行排序，显示指定目录中文件名及其属性详情信息
ls -Sl /etc
```

###### ll

* ll 命令的功能是显示指定文件或目录的详细属性信息。实际它不是一个真实存在的命令，只是“ls -l --color=auto”的别名而已。ll 命令可以默认列出当前目录内文件的详细属性信息，包含权限、所属、修改时间以及占用空间等信息

```shell
# 显示当前目录内文件的详细属性信息
ll

# 以文件上次被修改的时间排序，显示当前目录内文件的详细属性信息
ll -t

# 以更易读的容量单位显示文件大小
ll -h

# 查看某个指定文件的详细属性信息
anaconda-ks.cfg
```



###### cd

* 切换目录

```shell
# 切换到指定目录
cd /root/

# 切换到用户家目录
cd
cd ~

# 切换到上次目录
cd -

# 切换到上级目录
cd ..
```

###### mkdir

* 创建目录

```shell
# 创建单个目录
mkdir apple

# 创建多级目录
mkdir -p fruit/apple

# 创建一个目录文件并设置700权限，不让除所有主以外的任何人读、写、执行它
mkdir -m 700 Dir2

# 一次性创建多个目录文件
 mkdir Dir3 Dir4 Dir5
```

###### rmdir

* rmdir 命令来自英文词组 remove directory 的缩写，其功能是删除空目录文件。rmdir 命令仅能删除空内容的目录文件，如需删除非空目录时，需要使用带有-R 参数的 rm 命令进行操作。而 rmdir 命令的递归删除操作（-p 参数使用）并不意味着能删除目录中已有的文件，而是要求每个子目录都必须是空的

```shell
# 删除一个空目录
rmdir /apple

# 删除指定的空目录及其内部的子空目录
rmdir -p Dir

# 删除指定的空目录并显示删除的过程
rmdir -v Dir
```

###### tree

tree 命令的功能是以树状图形式列出目录内容，可帮助运维人员快速了解目录的层级
关系



```shell

# 显示当前工作目录下的文件层级情况
tree

# 以文件和目录的更改时间进行排序
tree -t

# 以带有相对路径的形式，显示当前工作目录下的文件层级情况
tree -f

# 只显示目录的层级关系情况
tree -d






```





#### 文件

###### touch

* touch 命令的功能是创建空文件与修改时间戳。如果文件不存在，则会创建一个空内容的文本文件；如果文件已经存在，则会对文件的 Atime（访问时间）和 Ctime（修改时间）进行修改操作，管理员可以完成此项工作，而普通用户只能管理主机的文件

```shell
# 创建空文件
touch dog.txt

# 结合通配符，创建多个指定名称的空文件
touch File{1..5}.txt

# 修改指定文件的查看时间和修改时间
touch -d "2023-05-18 15:44" File.cfg
```

###### cp

* cp命令来自英文单词copy的缩写，中文译为“复制”，其功能是复制文件或目录。cp命令能够将一个或多个文件或目录复制到指定位置，亦常用于文件的备份工作

```shell
# 复制文件
cp apple/hello.txt pear/
cp hello.txt world.txt

# 复制文件夹
cp -r apple/ pear/temp/

# 复制文件时，保留其原始权限及用户归属信息
cp -a File1.cfg File2.cfg

# 将指定文件复制到/etc 目录中，并覆盖已有文件，不进行询问
cp -f File1.cfg /etc

# 将多个文件一同复制到/etc目录中，如已有目标文件名称则默认询问是否覆盖
cp File1.cfg File2.cfg /etc
```

###### rm

* rm 命令来自英文单词 remove 的缩写，中文译为“消除”，其功能是删除文件或目录，一次可以删除多个文件，或递归删除目录及其内的所有子文件
* rm 也是一个很危险的命令，使用的时候要特别当心，尤其对于新手更要格外注意。例如，执行 rm -rf /*命令会清空系统中所有的文件，甚至无法恢复回来。所以我们在执行之前一定要再次确认在在哪个目录中、到底要删除什么文件，考虑好后再敲击 Enter 键，要时刻保持清醒的头脑

```shell
# 强制删除目录中的所有内容
# -r表示递归删除 -f表示强制执行删除操作 -v表示显示指令的执行过程
rm -rf apple/

# 强制删除当前工作目录内所有以.txt 为后缀的文件
rm -f *.txt

# 强制清空服务器系统内的所有文件（慎用！！！）
 rm -rf /*
```

###### mv

* mv 命令来自英文单词 move 的缩写，中文译为“移动”，其功能与英文含义相同，能够对文件进行剪切和重命名操作
* 需要留意它与复制命令的区别。cp 命令是用于文件的复制操作，文件个数是增加的，而 mv 则为剪切操作，也就是对文件进行移动（搬家）操作，文件位置发生变化，但总个数并无增加

```shell
# 重命名
mv dog.txt cat.txt

# 移动文件
mv apple/hello.txt pear/

# 将指定目录移动到/etc 目录中，并定义新的目录名称
mv Dir1 /etc/Dir2

# 将/home 目录中所有的文件都移动到当前工作目录中，若遇到文件已存在则直接覆盖
mv -f /home/* .
```

###### dd

* dd 命令来自英文词组 disk dump 的缩写，其功能是复制及转换文件。使用 dd 命令可以按照指定大小的数据块来复制文件，并在复制的过程中对内容进行转换

```shell
# 生成一个指定大小（500MB）的新文件
dd if=/dev/zero of=File count=1 bs=500M

# 复制指定文件的前 50 个字节
dd if=File1.cfg of=File2.cfg count=1 bs=50

# 复制指定文件的内容，并将所有字符转换成大写后输出到新文件中
dd if=File1.cfg of=File2.cfg conv=ucase
```

###### uniq

uniq 命令来自英文单词 unique 的缩写，中文译为“独特的、唯一的”，其功能是去除文件
中的重复内容行。uniq 命令能够去除掉文件中相邻的重复内容行，如果两端相同内容，但中
间夹杂了其他文本行，则需要先使用 sort 命令进行排序后再去重，这样保留下来的内容就都
是唯一的了

```shell
# 对指定的文件进行去重操作
uniq File

# 统计相同内容行在文件中重复出现的次数
uniq -c File

# 仅显示指定文件中没有存在完全相同内容行的信息
uniq -u File

# 仅显示指定文件中存在完全相同内容行的信息
uniq -d File



```



###### egrep

egrep 命令来自英文词组 extended global regular expression print 的缩写，其功能是在文件
内查找指定的字符串。egrep 命令的执行效果与 grep -E 相似，使用的参数也可以直接参考 grep
命令。egrep 命令改良了 grep 命令原有的一些字符串处理功能，支持的正则表达式规则更多



```shell
# 在某个文件中搜索包含指定关键词的行（单一关键词）
egrep 'root' File.cfg

# 在某个文件中搜索包含指定关键词的行并显示行号
egrep -n 'root|linuxprobe' File.cfg

# 在某个文件中搜索包含指定关键词的行，将匹配内容反选后输出到屏幕
egrep -v 'root|linuxprobe' File.cfg

# 在某个文件中搜索包含指定关键词的行（多个关键词，有任意一个即满足条件）
egrep 'root|linuxprobe' File.cfg






```













#### 查看

###### cat more less

| 命令 | 功能                          |
| ---- | ----------------------------- |
| cat  | 查看内容较少的纯文本文件      |
| more | 分页显示文本文件的内容        |
| less | 按需加载显示文件,适合超大文件 |

* cat

​	cat 命令来自英文词组 concatenate files and print 的缩写，其功能是在终端设备上显示文件内容

```shell
# 查看文件内容并显示行号
cat -n dog.txt

# 搭配空设备文件和输出重定向操作符，清空指定文件的内容
 cat /dev/null > anaconda-ks.cfg
 
# 持续写入文件内容，直到碰到 EOF 终止符后结束并保存
cat > anaconda-ks.cfg << EOF
> Hello,World
> Linux!
> EOF

# 搭配输出重定向操作符，将光盘设备制作成镜像文件
cat /dev/cdrom > rhel.iso
```

* more

more 命令的功能是分页显示文本文件的内容。如果文本文件中的内容较多较长，使用 cat命令读取后则很难看清，这时使用 more 命令进行分页查看就比较合适了，该命令可以把文本内容一页一页地显示在终端界面上，用户每按一次 Enter 键即向下一行，每按一次空格键即向下一页，直至看完为止

```shell
# 分页显示指定的文本文件内容
more File.cfg

# 先进行清屏操作，随后以每次 10 行内容的格式显示指定的文本文件内容
more -c -10 File.cfg

# 分页显示指定的文本文件内容，若遇到连续两行及以上空白行的情况，则以一行空白行显示
more -s File.cfg

# 从第 10 行开始，分页显示指定的文本文件内容
more +10 File.cfg


```



| 按键            | 功能         |
| --------------- | ------------ |
| sapce（空白键） | 向下翻一页   |
| enter           | 向下翻一行   |
| q               | 退出more显示 |
| ctrl+f          | 向下滚动一屏 |

* less

less 命令的功能是分页显示文件内容。Less 命令分页显示的功能与 more 命令很相像，但more 命令只能从前向后浏览文件内容，而 less 命令不仅能从前向后浏览（按 PageDown 键），还可以从后向前浏览（按 PageUp 键），更加灵活

| 按键            | 功能                                 |
| --------------- | ------------------------------------ |
| space（空白键） | 向下翻一页                           |
| pagedown        | 向下翻一页                           |
| pageup          | 向上翻一页                           |
| /`字符串`       | 向下搜索`字符串`:n向下查找,N向上查找 |
| ?`字符串`       | 向上搜索`字符串`:n向下查找,N向上查找 |
| q               | 离开less显示                         |

```shell
# 分页查看指定文件的内容
less File.cfg

# 分页查看指定文件的内容及行号
less -N File.cfg

# 分页显示指定命令的输出结果
history | less
```

###### top

* 显示文件前n行内容

```shell
# 默认查看前10行
top dog.txt

# 查看前20行
top -n 20 dog.txt
```

###### head

* head 命令的功能是显示文件开头的内容，默认为前 10 行

```shell
# 默认显示文件的前 10 行内容
head -n 10 File.cfg

# 显示指定文件的前 5 行内容
head -n 5 File.cfg

# 显示指定文件的前 20 个字符
head -c 20 File.cfg
```



###### tail

* tail 命令的功能是查看文件尾部内容，例如默认会在终端界面上显示指定文件的末尾 10行，如果指定了多个文件，则会在显示的每个文件内容前面加上文件名来加以区分。高阶玩法的-f 参数的作用是持续显示文件的尾部最新内容，类似于机场候机厅的大屏幕，总会把最新的消息展示给用户，对阅读日志文件尤为适合，再也不需要手动刷新了

```shell
# 模拟查看文件后10行
tail dog.txt

# 查看文件后20行
tail -n 20 dog.txt

# 实时追踪文档的更新
tail -f dog.txt

# 指定显示指定文件尾部的后 30 个字节
tail -c 30 File.cfg
```

###### wc

* wc 命令来自英文词组 word count 的缩写，其功能是统计文件的字节数、单词数、行数等信息，并将统计结果输出到终端界面。利用 wc 命令可以很快地计算出准确的单词数及行数，评估出文本的内容长度

```shell
# 统计指定文件的单词数量
wc -w File.cfg

# 统计指定文件的字节数量
wc -c File.cfg

# 统计指定文件的字符数量
 wc -m File.cfg
 
# 统计指定文件的总行数
wc -l File.cfg
```

###### sort

* sort 命令的功能是对文件内容进行排序。有时文本中的内容顺序不正确，一行行地手动修改实在太麻烦了。此时使用 sort 命令就再合适不过了，它能够对文本内容进行再次排序

```shell
# 对指定的文件内容按照字母顺序进行排序 对指定的文件内容按照数字大小进行排序
sort File.txt

# 以冒号（：）为间隔符，对指定的文件内容按照数字大小对第 3 列进行排序
sort -t : -k 3 -n File.txt



```

###### cut

* cut 命令的功能是按列提取文件内容。常用的 grep 命令仅能对关键词进行按行提取过滤，而 cut 命令则可以根据指定的关键词信息，针对特定的列内容进行过滤

```shell
# 以冒号为间隔符，仅提取指定文件中第一列的内容
cut -d : -f 1 /etc/passwd

# 仅提取指定文件中每行的前 4 个字符
cut -c 1-4 /etc/passwd

# 

```

###### diff

diff 命令来自英文单词 different 的缩写，其功能是比较文件内容的差异



```shell
# 仅判断两个文件是否不同
diff --brief File1.txt File2.txt Files File1.txt and File2.txt differ


# 比较两个文件内容的不同之处，定位所在行数
diff -c File1.txt File2.txt


# 




```











#### 搜索

###### find

* find命令的功能是根据给定的路径和条件查找相关文件或目录
* find 命令通常进行的是从根目录（/）开始的全盘搜索，有别于 whereis、which、locate 等有条件或部分文件的搜索。对于服务器负载较高的情况，建议不要在高峰时期使用 find 命令的模糊搜索，这会相对消耗较多的系统资源

```shell
# 在指定目录下根据名称查找文件
find fruit/ -name "*.txt"

# 在指定目录下根据用户查找文件
find fruit/ -user ming

# 在指定目录下根据大小查找文件（+n大于 -n小于 n等于）
find fruit/ -size +200

# 列出当前工作目录中的所有文件、目录以及子文件信息
find .

# 在/var/log 目录下搜索所有指定后缀的文件
find /var/log -name "*.log"

# 在/var/log 目录下搜索所有不是以.log 结尾的文件
find /var/log ! -name "*.log"

# 搜索当前工作目录中所有近7天被修改过的文件
find . -mtime +7
```

###### locate

* locate 命令的功能是快速查找文件或目录。与 find 命令进行全局搜索不同，locate 命令是基于数据文件（/var/lib/locatedb）进行的定点查找，由于缩小了搜索范围，因此速度快很多。要想让 locate 命令查询的结果更加准确，建议定期执行 updatedb 命令对数据库文件进行更新
* locate 参数 文件名

```shell
# 使用指令前创建locate数据库
updatedb

# 查找文件
# locate apple.txt
```

###### whereis



###### which



#### 查找

###### grep

* grep命令来自英文词组global search regular expression and print out the line的缩写，意思用于全面搜索的正则表达式，并将结果输出.把grep命令当作标准搜索命令，那么egrep则是扩展搜索命令，等价于grep -E命令，支持扩展的正则表达式。而fgrep则是快速搜索命令，等价于 grep -F 命令，不支持正则表达式，直接按照字符串内容进行匹配

```shell
# 搜索指定文件中包含某个关键词的内容行
grep root /etc/passwd

# 搜索指定文件中以某个关键词开头的内容行
grep ^root /etc/passwd

# 搜索多个文件中包含某个关键词的内容行
grep linuxprobe /etc/passwd /etc/shadow
 
# 搜索多个文件中包含某个关键词的内容，不显示文件名称
grep -h linuxprobe /etc/passwd /etc/shadow

# 显示指定文件中包含某个关键词的行数量
grep -c root /etc/passwd /etc/shadow

# 搜索指定文件中包含某个关键词位置的行号及内容行
grep -n network anaconda-ks.cfg

# 搜索指定文件中不包含某个关键词的内容行
grep -v nologin /etc/passwd

# 搜索当前工作目录中包含某个关键词内容的文件，未找到则提示
grep -l root *

# 搜索当前工作目录中包含某个关键词内容的文件，未找到也不提示
grep -sl root *
```

> ==管道符（|）:将前一个命令的标准输出传递给后一个命令的标准输入==
>
> `grep 选项 查找内容 源文件`
>
> * 选项
>   * ==-n:显示匹配行及行号==
>   * -c:只显示匹配的行数
>   * -h:搜索多文件时不显示文件名
>   * -i:忽略关键词大小写
>   * -l:只显示符合匹配条件的文件名
>   * -n:显示所有匹配行及其行号
>   * -E:支持扩展正则表达式 
>   * -F:匹配固定字符串的内容 

> * 

###### cut

> * 作用
>
>   cut命令从文件的每一行剪切字节、字符和字段并将这些字节、字符和字段输出
>
> * 参数
>
>   * ==-f:列号,提取第几列==
>   * ==-d:分隔符,按照指定分隔符分割列，默认是制表符“\t"==
>   * `-c:按字符进行切割,后加加n表示取第几列`

#### 编辑

###### sed

* sed命令来自英文词组stream editor的缩写，其功能是利用语法/脚本对文本文件进行批量的编辑操作

```shell
# 查找指定文件中带有某个关键词的行
cat -n File.cfg | sed -n '/root/p'

# 将指定文件中某个关键词替换成大写形式
sed 's/root/ROOT/g' File.cfg

# 读取指定文件，删除所有带有某个关键词的行
sed '/root/d' File.cfg

# 读取指定文件，在第 4 行后插入一行新内容
sed -e 4a\NewLine File.cfg

# 读取指定文件，在第 4 行后插入多行新内容
cat File.cfg | sed -e '4a NewLine1 \ NewLine2 \ NewLine3'

# 读取指定文件，删除第2～5行的内容
cat -n /etc/passwd | sed '2,5d'

# 读取指定文件，替换第2～5行的内容
sed '2,5c NewSentence' File.cfg

# 读取指定文件的第 3～7 行
sed -n '3,7p' File.cfg

# 
```

> 参数
>
> * -n:仅显示脚本处理后的结果
> * -i:直接修改文件内容，而不输出到终端
> * -e:使用指定脚本处理输入的文本文件

###### awk

* awk 命令来自三位创始人 Alfred Aho、Peter Weinberger、Brian Kernighan 的姓氏缩写，其功能是对文本和数据进行处理



> * 作用
>
>   一个强大的文本分析工具，把文件逐行的读入，以空格为默认分隔符将每行切片，切开 的部分再进行分析处理
>
> * 语法
>
>   ==awk [选项参数] 'pattern1/action1' 'pattern2/action2' filename==
>
>   * 选线参数
>     * -F:指定输入文件分隔符
>     * -v:赋值一个用户定义变量
>
>   * pattern:表示awk在数据中查找的内容，就是匹配模式
>   * action:在找到匹配内容时所执行的一系列命令
>
> * 内置变量
>
>   * FILENAME:文件名
>   * NR:已读的记录数
>   * NF:浏览记录的域的个数（（切割后，列的个数）
>
> * 命令使用
>
>   [awk命令详解]（）

###### tr

* tr 命令来自英文单词 transform 的缩写，中文译为“转换”，其功能是转换字符。tr 命令是一款批量字符转换、压缩、删除的文本工具，但仅能从标准输入中读取文本内容，需要与管道符或输入重定向操作符搭配使用

```shell
# 将指定文件中的小写字母转换成大写字母后输出内容到终端界面
tr [a-z] [A-Z] < File.cfg

# 删除指定文件中所有的数字后输出内容到终端界面
tr -d [0-9] < File.cfg

# 将指定文件中的多个相邻空行去重后输出内容到终端界面
tr -s "[\n]" < File.cfg


```







#### 输出

###### > >> 

| 语法 | 功能                   |
| ---- | ---------------------- |
| `>`  | 覆盖输出（输出重定向） |
| `>>` | 追加输出               |

###### echo

​	echo 命令的功能是在终端设备上输出指定字符串或变量提取后的值，能够给用户一些简单的提醒信息，亦可以将输出的指定字符串内容同管道符一起传递给后续命令作为标准输入信息进行二次处理，还可以同输出重定向符一起操作，将信息直接写入文件。如需提取变量值，需在变量名称前加入$符号，变量名称一般均为大写形式

```shell
# 输出指定字符串到终端设备界面（默认为电脑屏幕）
echo LinuxCool

# 输出某个变量值
echo $PATH

# 搭配输出重定向符一起使用，将字符串内容直接写入文件中
echo "Hello World" > Doc.txt

# 搭配反引号执行命令，并将执行结果输出
echo `uptime`

# 输出带有换行符的内容
 echo -e "First\nSecond\nThird"
 
# 指定删除字符串中某些字符，随后将内容输出
echo -e "123\b456"

# 换行符
echo -e aa\\nbb

# 制表符
echo -e aa\\tbb
```









#### 其他

###### ln

* ln 命令来自英文单词 link 的缩写，中文译为“链接”，其功能是为某个文件在另外一个位置建立同步的链接。Linux 系统中的链接文件有两种形式，一种是硬链接，另一种是软链接。软链接相当于 Windows 系统中的快捷方式文件，原始文件被移动或删除后，软链接文件也将无法使用；硬链接则是将文件的 inode 属性块进行了复制，因此把原始文件移动或删除后，硬链接文件依然可以使用

```shell
# 为指定的源文件创建快捷方式（默认为硬链接形式）
ln File1.cfg File2.cfg

# 为指定的源文件创建快捷方式，并输出制作的过程信息
ln -v File1.cfg File2.cfg

# 为指定的源文件创建快捷方式（设定为软链接形式）
ls -s apple/ apple-link

# 进入软连接对应的目录
cd -P apple-link/
```

> * 查看软连接
>
>   通过ll就可以查看，列表属性第1位是l，尾部会有位置指向
>
> * 删除软连接
>
>   `rm -rf 软链接名`，而不是==rm -rf 软链接名/==,如果使用==rm -rf 软链接名/==删除，会把软链接对应的真实目录下内容删掉

###### file

* file 命令的功能是识别文件类型，也可以用来辨别一些内容的编码格式。由于 Linux 系统并不是像 Windows 系统那样通过扩展名来定义文件类型的，因此用户无法直接通过文件名来进行区别。file 命令可以通过分析文件头部信息中的标识信息来显示文件类型，使用很方便

```shell
# 查看指定文件的类型
file File.cfg

# 查看指定文件的类型，但不显示文件名
file -b File.cfg

# 通过 MIME 来分辨指定文件的类型
file -i File.cfg

# 查看符号链接文件的类型，会提示实际的文件名称
file /dev/cdrom

# 直接查看指定符号链接文件所对应的目标文件的类型
file -L /dev/cdrom
```







### 压缩命令

#### zip

​	zip 命令的功能是压缩文件，解压命令为 unzip。通过 zip 命令可以将文件打包成 zip 格式的压缩包，里面包含文件的名称、路径、创建时间、上次修改时间等信息（与 tar 命令相似）。

> 1. 可以压缩文件和目录
> 2. 保留源文件
> 3. 文件后缀为==.zip==

###### zip

```shell
# 压缩文件
zip fruit.zip apple.txt pear.txt

# 将指定目录及其包含的全部文件都打包成 zip 格式的压缩包文件
zip -r File.zip /etc

# 将当前工作目录内所有以.cfg 为后缀的文件打包
zip -r File.zip *.cfg

# 更新压缩包文件中的某个文件
zip -dv File.zip File.cfg
```

###### unzip

```shell
# 将压缩包文件解压到当前工作目录中
unzip fruit.zip

# 解压文件到指定目录
unzip fruit.zip -d /ming

# 测试压缩包文件是否完整，文件有无损坏
unzip -t File.zip
```

#### unrar

unrar命令来自英文词组unzip rar的缩写，其功能是解压提取 RAR 压缩文件。该命令轻松解压来自 Windows 系统的 rar 压缩包文件

```shell
# 以完整路径解压指定压缩包文件
unrar x File.rar

# 查看指定压缩包内的文件信息
unrar l File.rar

# 测试指定压缩包内文件是否损坏，能够正常解压
unrar t File.rar

# 解压指定压缩包内的文件到当前工作目录
unrar e File.rar
```

#### gzip

gzip 命令来自英文单词 gunzip 的缩写，其功能是压缩和解压文件。gzip 是一个使用广泛的压缩命令，文件经过压缩后一般会以.gz 后缀结尾，与 tar 命令合用后即为.tar.gz 后缀。据统计，gzip 命令对文本文件的压缩比率通常能达到 60%~70%，压缩后可以很好地提升存储空间的使用率，还能够在网络中传输文件时减少等待时间

> 1. 只能将文件压缩成*.gz文件
> 2. 只能压缩文件,不能压缩目录
> 3. 不保留原来的文件
> 4. 同时压缩多个文件会产生多个压缩包
> 5. 文件后缀为==.gz==

###### gzip

```shell
# 压缩文件
gzip apple.txt

# 将指定的文件进行压缩，压缩包默认以“原文件名.gz”保存到当前工作目录下，原文件会被自动删除
gzip File.cfg

# 解压指定的压缩包文件并显示解压过程，解压后的文件会保存在当前工作目录下，压缩包会被自动删除
gzip -dv File.cfg.gz

# 将指定的文件进行压缩，但是不删除原文件
gzip -k File.cfg

# 显示指定文件的压缩信息
gzip -l File.cfg.gz
```

###### gunzip

```shell
# 解压指定的压缩包文件
gunzip File.gz

# 解压指定的压缩包文件，并输出解压过程
gunzip -v File.gz

# 测试指定的压缩包文件内容是否损坏，是否能够正常解压
gunzip -t File.gz
```

#### tar

​	tar 命令的功能是压缩和解压缩文件，能够制作出 Linux 系统中常见的 tar、tar.gz、tar.bz2等格式的压缩包文件。对于 RHEL 7、CentOS 7 版本及以后的系统，解压缩时不添加格式参数（如 z 或 j），系统也能自动进行分析并解压。把要传输的文件先压缩再传输，能够很好地提高工作效率，方便分享

> 1. 归档并压缩
> 2. 文件后缀为==.tar.gz==

```shell
# 压缩文件
tar -zcvf fruit.tar.gz apple.txt pear.txt

# 使用 gzip 压缩格式对指定目录进行打包操作，显示压缩过程，压缩包规范后缀为.tar.gz
tar czvf File.tar.gz /etc

# 使用bzip2压缩格式对某个目录进行打包操作，显示压缩过程，压缩包规范后缀为.tar.bz2
tar cjvf File.tar.bz2 /etc

# 将当前工作目录内所有以.cfg 为后缀的文件打包，不进行压缩
tar cvf File.tar *.cfg

# 将当前工作目录内所有以.cfg 为后缀的文件打包，不进行压缩，并删除原始文件
tar cvf File.tar *.cfg --remove-files

# 解压指定压缩包到当前工作目录
tar xvf File.tar

# 解压指定压缩包到/etc 目录
tar xvf File.tar -C /etc

# 查看某个压缩包内文件信息（无须解压）
tar tvf File.tar

# 解压文件
tar -zxvf fruit.tar.gz

# 解压文件到指定目录
tar -zxvf fruit.tar.gz -C /ming
```

#### bzip2

bzip2 命令的功能是压缩或解压缩.bz2 文件。Linux 系统中常见的以.bz2 结尾的文件是由bzip2 命令压缩而成的，bzip2 是一款压缩算法较新、压缩比较充分的压缩工具，与 gzip 命令十分相似

```shell
# 对指定的文件进行压缩操作
 bzip2 File.cfg
 
# 对指定的文件进行压缩操作，并显示详细过程
bzip2 -v File.cfg

# 检查指定压缩包文件的完整性
bzip2 -t File.bz2

# 解压缩指定的压缩包文件
bzip2 -d File.bz2
```

#### pigz

​	pigz 命令来自英文词组 parallel implementation of gzip 的缩写，其功能是认多线程的方式解压缩文件。与其他解压缩命令不同的是，pigz 命令支持多线程的并行处理方式，比 gzip 快60%以上，当然 CPU 的消耗也会更高。如果想快速地压缩、解压文件，那么就一定要选它

```shell
# 对已打包好的指定文件进行压缩
pigz File.tar

# 查看指定文件的压缩比率信息
pigz -l File.tgz

# 解压指定的文件，线程设定为 8 个
pigz -d -p 8 File.tgz
```

#### 7z

7z 命令的功能是对文件进行解压缩操作。7-z 命令是 Linux 系统中常用的解压缩工具，7z 也是一种压缩格式，具备较高的压缩比率，对文本文件尤其有效

```shell
# 对指定的目录进行压缩，压缩包以 7z 为后缀
7z a File.7z /Dir

# 对指定的压缩包文件进行解压缩
7z x File.7z

# 将指定压缩包内的以 txt 结尾的文件都删除
7z d File.7z -r *.txt

# 更新指定压缩包内的以 txt 结尾的文件
7z u File.7z *.txt

```

###### zipinfo

zipinfo 命令来自英文词组 zip information 的缩写，其功能是查看压缩文件信息。使用
zipinfo 命令可以查看 zip 格式压缩包内的文件列表及详细信息



```shell
# 显示压缩包内的文件名称及简要属性信息
zipinfo File.zip

# 显示压缩包内的文件名称及详细属性信息
zipinfo -v File.zip

# 仅显示压缩包内的文件大小及数目信息
zipinfo -h File.zip

# 仅显示压缩包内的文件最后修改时间及简要属性信息
zipinfo -T File.zip





```









### 软件命令

#### CentOS

###### rpm

​	rpm 命令来自英文词组 redhat package manager 的缩写，中文译为“红帽软件包管理器”，其功能是在 Linux 系统下对软件包进行安装、卸载、查询、验证、升级等工作，常见的主流系统（如 RHEL、CentOS、Fedora 等）都采用这种软件包管理器，推荐用固定搭配“rpm-ivh 软件包名”安装软件，而卸载软件则用固定搭配“rpm -evh 软件包名”，简单好记又好用

```shell
# 正常安装软件包
rpm -ivh cockpit-185-2.el8.x86_64.rpm

# 显示系统已安装过的全部 RPM 软件包
rpm -qa

# 查询某个软件的安装路径
rpm -ql cockpit

# 卸载通过 RPM 软件包安装的某个服务
rpm -evh cockpit

# 升级某个软件包
rpm -Uvh cockpit-185-2.el8.x86_64.rpm
```



###### yum

* yum 命令来自英文词组 yellow dog updater modified 的缩写，其功能是在 Linux 系统中基于 RPM 技术进行软件包的管理工作。yum 技术通用于 RHEL、CentOS、Fedora、OpenSUSE等主流系统，可以让系统管理人员交互式地自动化更新和管理软件包，实现从指定服务器自动下载、更新、删除软件包的工作。yum 软件仓库及命令能够自动处理软件依赖关系，一次性安装所需的全部软件，无须烦
  琐的操作

```shell
# 清理原有的软件仓库信息缓存
yum clean all

# 建立最新的软件仓库信息缓存
yum makecache

# 安装指定的服务及相关软件包
yum install -y httpd 

# 更新指定的服务及相关软件包
yum update httpd

# 卸载指定的服务及相关软件包
yum remove httpd

# 显示可安装的软件包组列表
yum grouplist

# 显示指定服务的软件信息
yum info httpd
```

#### Ubuntu

###### apt-get

* apt-get 命令来自英文词组 advanced package tool get 的缩写，其功能是管理服务软件。apt-get 命令主要用于 Debian、Ubuntu 等系统，能够像 yum/dnf 软件仓库一样自动下载、配置、安装、卸载服务软件，用户只要准确提出需求就好

```shell
# 安装指定的服务软件
apt-get install httpd

# 更新软件列表
apt-get update

# 卸载指定的服务软件
apt-get remove httpd

# 卸载指定的服务软件及配置信息
apt-get -purge remove httpd



```

###### dpkg

* dpkg 命令来自英文词组 Debian package 的缩写，其功能是管理软件安装包，是在 Debian系统中最常用的软件安装、管理、卸载的实用工具

```shell
# 安装指定的软件包
dpkg -i File.deb

# 卸载指定的软件包
dpkg -r File.deb

# 列出当前已安装的软件列表
dpkg -l

# 显示指定软件包内的文件信息
dpkg -c File.deb


```







#### Gcc

###### gcc

* gcc 命令来自英文词组 GNU Compiler Collection 的缩写，是 C/C++语言编译器。gcc 是开源领域使用最广泛的编译工具，具有功能强大、兼容性强、效率高等特点。编译工作由 4 个阶段组成：预编译（Preprocessing）、编译（Compilation）、汇编（Assembly）、链接（Linking）

```shell
# 编译指定的源码文件
gcc File.c

# 编译指定的源码文件，并生成可执行文件
gcc File.c -o linux

# 对指定的源码文件进行预处理
gcc -E File.c -o linux.i

# 对预处理后的指定文件进行汇编操作
gcc -S File.i -o linux.s

# 对汇编处理后的指定文件进行编译操作
gcc -c File.s -o linux.o

# 对编译处理后的指定文件进行链接操作
gcc File.o -o linux




```

#### 安装编译

###### make

* make 命令的功能是编译内核或源码文件。make 是 GNU 工程化编译工具，用于编译众多相互关联的源代码文件。make 命令也可以编译内核或模块功能，以工程化的工作方式提高开发效率。初次运行 make 命令时，它会通过扫描 Makefile 文件找到目标及其依赖关系，并在建立依赖关系后依次编译所对应的源码程序

```shell
# 编译当前工作目录下的工程源码
make

# 读取指定文件作为 Makefile 文件
make -f Makefile

# 为 make 命令提供指定的不同目录路径
make -C /Dir

# 显示全部的调试信息
make -d
```









### 网络命令

#### 查看

###### ifconfig

* ifconfig 命令来自英文词组network interfaces configuring的缩写，其功能是显示或设置网络设备参数信息。在Windows 系统中，与之类似的命令为ipconfig，同样的功能可以使用ifconfig 去完成。通常不建议使用 ifconfig 命令配置网络设备的参数信息，因为一旦服务器重启，配置过的参数会自动失效，因此还是编写到配置文件中更稳妥

```shell
# 显示系统的网络设备信息
ifconfig

# 对指定的网卡设备依次进行关闭和启动操作
ifconfig ens160 down
ifconfig ens160 up

# 对指定的网卡设备执行 IP 地址修改操作
ifconfig ens160 192.168.10.20 netmask 255.255.255.0

# 对指定的网卡设备执行 MAC 地址修改操作
ifconfig ens160 hw ether 00:aa:bb:cc:dd:ee

# 对指定的网卡设备依次进行 ARP 协议关闭和开启操作
ifconfig ens160 -arp
```



###### ping

* 测试网络连通性

```shell
# 测试与指定域名之间的网络连通性（需手动按下 Ctrl+C 组合键结束命令）
ping www.baidu.com

# 测试与指定主机之间的网络连通性，发送请求包限定为 4 个
ping -c 4 192.168.10.10

# 测试与指定域名之间的网络连通性，发送请求包限定为 4 个
ping -c 4 www.linuxcool.com

# 测试与指定主机之间的网络连通性，发送 3 个请求包，每次间隔 0.2s，最长等待时间为 3s
 ping -c 3 -i 0.2 -W 3 192.168.10.10
```

###### hostname

* 修改主机名称`

```shell
# 查看主机名
hostname

# 修改主机名
vim /etc/hostname
hostnamectl set-hostname 主机名

# 修改hosts映射文件
vim /etc/hosts
```

###### uname

* 查看内核版本

```shell
# 详细信息
uname -a
```

###### netstat

* netstat 命令来自英文词组 network statistics 的缩写，其功能是显示各种网络相关信息，例如网络连接状态、路由表信息、接口状态、NAT、多播成员等
* netstat 命令不仅应用于 Linux 系统，而且 Windows XP、Windows 7、Windows 10 及Windows 11 均已默认支持，并且可用参数也相同，有经验的运维人员可以直接上手

> 参数
>
> * a:显示所有正在监听（listen）和未监听的套接字（socket）
> * n:拒绝显示别名，能显示数字的全部转化成数字
> * l:仅列出在监听的服务状态
> * p:表示显示哪个进程在调用

```shell
# 显示系统网络状态中的所有连接信息
netstat -a

# 显示系统网络状态中的 UDP 连接信息
netstat -nu

# 显示系统网络状态中的 UDP 连接端口号使用信息
netstat -apu

# 显示网卡当前状态信息
netstat -i

# 显示网络路由表状态信息
netstat -r

# 查看指定进程的网络信息
netstat -anp | grep sshd

# 查看网络端口的占用情况
netstat -nlp | grep 22
```

###### ip

* ip 命令的功能是显示与配置网卡参数。作为 Linux 系统下一款好用的网卡参数配置工具，ip 命令除了常规操作外，还可以对主机的路由、网络设备、策略路由以及隧道信息进行查看

```shell
# 显示当前网络设备的运行状态
ip link

# 显示当前网络设备的详细运行状态
ip -s link

# 显示当前核心路由表信息
ip route list

# 显示当前 IP 地址信息
ip address
```

###### iperf

* iperf 是一款用于测试网络性能的命令工具，由美国伊利诺伊大学研发和维护，可以用来测试一些网络设备（如路由器、防火墙、交换机等）的性能。性能测试是在服务器已经启动服务的场景下进行的，服务器需先执行 iperf -s 或 iperf3 -s命令

```shell
# 客户端向服务器发起累计 10 秒、每秒数据包为 100MB 的请求
iperf -c 192.168.10.10 -b 100M -t 10

# 基于默认的 TCP 协议，测试客户端到服务器的上传速度
iperf -c 192.168.10.10 -t 10
```

###### route

* route 命令的功能是显示与设置路由信息，是 Linux 系统中常用的静态路由配置工具。要想让两台处于不同子网的服务器实现通信，需要有一个跨网段的路由器来连接它们，并用route命令为其设置路由信息

```shell
# 显示当前路由表信息
route

# 添加一条指定的路由信息
route add -net 192.168.10.0 netmask 255.255.255.0 dev ens160

# 删除一条指定的路由信息
route del -net 192.168.10.0 netmask 255.255.255.0 dev ens160

# 添加和删除默认网关
route add default gw 192.168.10.1
route del default gw 192.168.10.1



```







#### 设置

###### dhclient

* dhclient 命令来自英文词组 DHCP client 的缩写，其功能是动态获取或释放 IP 地址。使用dhclient 命令前，需要将网卡模式设置成 DHCP 自动获取，否则静态模式的网卡不会主动向服务器获取如 IP 地址等网卡信息。

```shell
# 通过指定网卡发起 DHCP 请求，获取网卡参数
dhclient ens160

# 释放系统中已获取的网卡参数
dhclient -r

# 向指定的服务器请求获取网卡参数
dhclient -s 192.168.10.10

# 手动停止执行 dhclient 服务进程
dhclient -x
```

###### hostnamectl

* hostnamectl命令来自于英文词组hostname control的缩写，其功能是显示与设置主机名称。基于/etc/hostname 文件修改主机名称时，需要重启服务器后才可生效，而使用 hostnamectl 命令设置过的主机名称可以立即生效，效率更高

```shell
# 显示当前系统的主机名称及系统信息
hostnamectl status

# 修改当前系统的主机名称为指定字符串
hostnamectl set-hostname linuxcool.com
```



###### nmcli

nmcli 命令来自英文词组 networkmanager command-line interface 的缩写，其功能是基于命
令行配置网卡参数。使用nmcli与nmtui命令工具配置过的参数会直接写入网卡服务配置文件，
并永久生效





```shell
# 显示所有网络连接的列表
nmcli con show


显示所有网络连接的详细信息
nmcli device show

# 对指定网卡创建一个网络会话连接，网卡参数通过DHCP 服务获取
nmcli connection add con-name house type ethernet ifname ens160

# 对指定网卡创建一个网络会话连接，网卡参数由手动指定配置
nmcli connection add con-name company ifname ens160 autoconnect notype ethernet ip4 192.168.10.10/24 gw4 192.168.10.1

# 对一个指定的网络会话连接添加 DNS 地址参数
nmcli connection modify company ipv4.dns 8.8.8.8

```









#### 远程服务

###### ssh

* ssh 命令的功能是安全地远程连接服务器主机系统，作为 OpenSSH 套件中的客户端连接工具，ssh 命令可以让我们轻松地基于 SSH 加密协议进行远程主机访问，从而实现对远程服务器的管理工作

```shell
# 基于 SSH 协议，远程访问服务器主机系统
ssh 192.168.10.10

# 使用指定的用户身份登录远程服务器主机系统
ssh -l linuxprobe 192.168.10.10

# 登录远程服务器主机系统后执行一条命令
ssh 192.168.10.10 "free -m"

# 强制使用 v1 版本的 SSH 加密协议连接远程服务器主机
ssh -1 192.168.10.10
```

###### tftp

* tftp 命令来自英文词组 Trivial File Transfer Protocol 的缩写，中文译为“简单文件传输协议”，其功能是基于 TFTP 进行文件传输工作。用户可以通过文字模式将文件上传至远程服务器，亦可以从服务器下载文件到本地主机。TFTP 基于 UDP/69，不同于 FTP，属于轻量级的传输服务，不具备显示文件列表、断点续传等功能

```shell
# 远程连接至指定服务器
tftp 192.168.10.10

# 下载远程指定服务器中的文件至本地工作目录
tftp> get File1.txt

# 上传本地工作目录中某个文件至远程指定服务器
tftp> put File2.txt

# 退出登录某台远程服务器

# tftp> quit
```

###### scp

* scp 命令来自英文词组 secure copy 的缩写，其功能是基于 SSH 协议远程复制文件。scp命令可以在多台 Linux 系统之间复制文件或目录，它有些类似于 cp 命令的功能，但复制的范围却不是本地，而是网络上的另一台主机。由于 scp 命令是基于 SSH 协议进行的复制操作，全部数据都是加密的，因此会比 HTTP和 FTP 更加安全

```shell
# 将某个本地文件复制到指定的远程主机的指定目录中
scp File.cfg 192.168.10.10:/Dir

# 将指定远程主机中的某个文件复制到本地家目录中
scp 192.168.10.10:/Dir/File.cfg /root

# 将某个本地目录复制到指定的远程主机的指定目录中
scp -r Dir 192.168.10.10:/Dir

# 将指定远程主机中的某个目录复制到本地家目录中
scp -r 192.168.10.10:/Dir /root

# 将某个本地文件复制到指定的远程主机的指定目录中，指定要使用的传输用户身份，并保留原始文件的权限属性
scp -p File.cfg linuxprobe@192.168.10.10:/Dir
```

###### ssh-copy-id

* ssh-copy-id 命令来自英文词组 ssh copy id，其功能是将本地生成的 SSH 公钥信息复制到远程主机。通常情况下，运维人员会先使用 ssh-keygen 命令生成 SSH 密钥对（公钥/私钥）文件，随后使用 ssh-copy-id 命令将公钥文件复制到远程主机上，这样操作后，再进行远程 SSH访问时将无须进行账号密码验证，而是通过密钥方式登录

```shell
# 将 SSH 公钥文件复制到远程主机
ssh-copy-id 192.168.10.10
```



#### 下载

###### curl

* curl命令来自英文词组CommandLine URL的缩写，其功能是在Shell终端界面中基于URL规则进行文件传输工作。curl 是一款综合性的传输工具，可以上传也可以下载，支持 HTTP、HTTPS、FTP 等 30 余种常见协议

```shell
# 获取指定网站的网页源码
curl https://www.linuxcool.com

# 下载指定网站中的文件
curl -O https://www.linuxprobe.com/docs/LinuxProbe.pdf

# 打印指定网站的 HTTP 响应头信息
curl -I https://www.linuxcool.com

# 下载指定文件服务器中的文件（用户名:密码）
curl -u linuxprobe:redhat ftp://www.linuxcool.com/LinuxProbe.pdf
```

###### wget

* wget 命令来自英文词组 web get 的缩写，其功能是从指定网址下载网络文件。wget 命令非常稳定，一般即便网络发生波动也不会导致下载失败，而是不断地尝试重连，直至整个文件下载完毕。wget 命令支持如 HTTP、HTTPS、FTP 等常见协议，可以在命令行中直接下载网络文件

```shell
# 下载指定的网络文件
wget https://www.linuxprobe.com/docs/LinuxProbe.pdf

# 下载指定的网络文件，并定义保存在本地的文件名称
wget -O Book.pdf https://www.linuxprobe.com/docs/LinuxProbe.pdf

# 下载指定的网络文件，限速最高每秒 300kbit/s
wget --limit-rate=300k https://www.linuxprobe.com/docs/LinuxProbe.pdf

# 启用断点续传技术下载指定的网络文件
wget -c https://www.linuxprobe.com/docs/LinuxProbe.pdf

# 下载指定的网络文件，将任务放至后台执行
wget -b https://www.linuxprobe.com/docs/LinuxProbe.pdf
```

###### sz

* sz 命令来自英文词组 send Zmodem 的缩写，其功能是基于Zmodem 协议从远程服务器下载文件到本地。当我们在使用XShell、SecureCRT、PuTTY 等虚拟终端软件时，可以使用 sz 命令将远程文件直接下载到本地，在软件弹出的窗口选择本地保存路径即可。
  若您的系统中找不到 sz 与 rz 命令，请配置好软件仓库后执行 yum install lszrz 命令即可

```shell
# 下载指定的某个文件
sz File.cfg

# 以文本方式批量下载指定的多个文件
sz -a /Dir/*

# 以二进制方式下载指定的某个文件
sz -b File.tar.gz
```

###### rz

* rz 命令来自英文词组 receive Zmodem 的缩写，其功能是基于 Zmodem 协议上传文件到服务器。当我们在使用 XShell、SecureCRT、PuTTY 等虚拟终端软件时，可以使用 rz 命令将本地文件上传到服务器，方法是直接输入 rz 命令后选择要上传的文件即可

```shell
# 上传指定的文件到服务器,弹出窗口中选择要上传的文件即可
rz

# 上传指定的文件到服务器，若遇到重名的文件则直接覆盖
rz -y

# 上传指定的文件到服务器，遇到重名的文件也不要覆盖
rz -p
```



### 磁盘命令

#### 查看

###### df

```shell
# 显示系统全部磁盘的使用量情况（带容量单位）
df -h

# 显示指定磁盘分区的使用量情况（带容量单位）
df -h /boot

# 显示系统中所有文件系统格式为 XFS 的磁盘分区的使用量情况
df -t xfs
```

###### lsblk

* lsblk 命令来自英文词组 list block devices 的缩写，其功能是查看系统的磁盘使用情况

```shell
# 显示文件系统信息
lsblk -f

# 显示系统中所有磁盘设备的使用情况信息
lsblk -a

# 显示系统中磁盘设备的归属及权限信息
lsblk -m

# 显示系统中所有 SCSI 类型的磁盘设备信息
lsblk -S
```

###### du

* du 命令来自英文词组 disk usage 的缩写，其功能是查看文件或目录的大小。人们经常会把 df 和 du 命令混淆，df 是用于查看磁盘或分区使用情况的命令，而 du 命令则是用于按照指定容量单位来查看文件或目录在磁盘中的占用情况

```shell
# 以易读的容量格式显示指定目录内各个文件的大小信息
du -h /etc

# 以易读的格式显示指定目录内总文件的大小信息
du -sh /Dir

# 显示指定文件的大小信息（默认单位为 KB）
du File.cfg
```

###### hdparm

* hdparm 命令来自英文词组 hard disk parameters 的缩写，其功能是显示与设定硬盘参数。在初次接手一块硬盘设备时，了解相关性能属性会有很好的帮助

```shell
# 显示指定硬盘的相关信息
hdparm /dev/sdb

# 仅显示指定硬盘的柱面、磁头和扇区数信息
hdparm -g /dev/sdb

# 评估指定硬盘的读取效率
hdparm -t /dev/sdb

# 读取指定硬盘所提供的硬件规格信息
hdparm -X /dev/sdb



```



###### mkfs

mkfs 命令来自英文词组 make file system 的缩写，其功能是对设备进行格式化文件系统操
作。在挂载使用硬盘空间前的最后一步，运维人员需要对整块硬盘或指定分区进行格式化文
件系统操作。Linux 系统支持的文件系统包含 EXT2、EXT3、EXT4、XFS、FAT、MS-DoS、
VFAT、Minix 等多种格式



```shell
# 对指定的硬盘进行格式化文件系统操作
mkfs -t ext4 /dev/sdb

# 对指定的硬盘进行格式化文件系统操作，并输出详细过程信息
mkfs -V -t xfs /dev/sdb

# 


```









#### 分区

###### fdisk

* fdisk 的意思是固定磁盘（fixed disk）或格式化磁盘（format disk），该命令的功能是管理
  磁盘的分区信息。fdisk 命令可以用来对磁盘进行分区操作，用户可以根据实际情况对磁盘进行合理划分，
  这样后期挂载和使用时会方便很多。
* 语法格式：fdisk 参数 设备名

```shell
# 查看当前系统的分区情况
fdisk -l

# 管理指定硬盘的分区
fdisk /dev/sda
```



#### 挂载

###### mount

* mount 命令的功能是将文件系统挂载到目录。文件系统指的是被格式化过的硬盘或分区设备，进行挂载操作后，用户便可以在挂载目录中使用硬盘资源了
* 语法格式:mount 参数 设备名 目录名

```shell
# 查看当前系统中已有的文件系统信息
mount

# 挂载/etc/fstab 文件中所有已定义的设备文件
mount -a

# 将光盘设备挂载到指定目录
mount /dev/cdrom /Dir

# 强制以 XFS 文件系统挂载硬盘设备到指定目录
mount -t xfs /dev/sdb /Dir

# 卸载设备
```

###### umount

* umount 命令的功能是卸载文件系统。与 mount 挂载命令需要同时提供设备名与挂载目录不同，umount 卸载命令只需要提供设备名或挂载目录之一即可

```shell
# 卸载指定的文件系统
umount /dev/sdb

# 卸载指定的文件系统并显示过程
umount -v /dev/cdrom
```



###### mount.nfs 

* mount.nfs 命令来自英文词组 mount network file system 的缩写，其功能是挂载网络文件系统（NFS）。其实从 RHEL/CentOS 6 版本开始，挂载 NFS 已经可以使用 mount 命令直接完成了。如果系统版本比较老，NFS 版本为 v2 或 v3，则可以用 mount.nfs 命令来完成挂载操作

```shell
# 挂载指定的远程 NFS 服务器共享目录到本地
mount.nfs 192.168.10.10:/Dir /Dir

# 以只读方式挂载指定的远程 NFS 服务器共享目录到本地
mount.nfs -r 192.168.10.10:/Dir /Dir

# 以详细信息模式挂载指定的远程NFS 服务器共享目录到本地
mount.nfs -v 192.168.10.10:/Dir /Dir
```





#### 格式化

###### mkfs.ext4

* mkfs.ext4命令来自英文词组make filesystem Ext4的缩写，其功能是对磁盘设备进行EXT4格式化操作

```shell
# 检查指定的磁盘设备并进行格式化操作
mkfs.ext4 -c /dev/sdb

# 对指定的磁盘设备直接进行格式化操作
mkfs.ext4 /dev/sdb

# 对指定的磁盘设备进行格式化操作，保留 5%容量给管理员
mkfs.ext4 -m 5 /dev/sdb

# 对指定的磁盘设备进行格式化操作，添加卷标识，并修改块大小
mkfs.ext4 -L 'LinuxCool' -b 2048 /dev/sdb
```

#### 同步

###### resize2fs

* resize2fs 命令来自英文词组 resize to filesystem 的缩写，其功能是同步文件系统容量到内核。如对 EXT3、EXT4、XFS 等设备卷容量进行了调整，则需要使用 resize2fs 命令同步信息到系统内核

```shell
# 同步文件系统容量信息到系统内核
resize2fs /dev/sdb

# 同步文件系统容量信息到系统内核，并显示百分比进度条
resize2fs -p /dev/sdb

# 强制同步系统容量信息到系统内核
resize2fs -f /dev/sdb

# 刷新文件系统设备的缓冲区，随后同步容量信息到系统内核
resize2fs -F /dev/sdb
```

#### 逻辑卷

###### lvcreate

* lvcreate 命令的功能是创建逻辑卷设备。LVM（逻辑卷管理器）由物理卷、卷组和逻辑卷组成，其中 lvcreate 命令属于 LVM 创建工作的最后一个环节——创建逻辑卷设备。在设定逻辑卷容量时，可以使用-L 参数直接写具体值，亦可以使用-l 参数指定 PE（物理扩展块）个数，每个 PE 大小默认为 4MB，因此-L 400M 和-l 100 的效力是等价的

```shell
# 在已有的卷组中（VG01）创建一个逻辑卷（V001），大小为 100 个 PE
lvcreate -n V001 -l 100 VG01

# 在已有的卷组中（VG01）创建一个逻辑卷（V001），大小为400MB
lvcreate -n V001 -L 400 VG01

# 在已有的卷组中（VG01）创建一个逻辑卷（V001），大小为卷组中剩余的全部空间
lvcreate -n V001 -l 100%FREE VG01


```

###### pvcreate

* pvcreate 命令的功能是创建物理卷设备。LVM（逻辑卷管理器）由物理卷、卷组和逻辑卷组成，其中 pvcreate 命令属于 LVM 创建工作的第一个环节 — 创建物理卷设备

```shell
#　将指定的某个磁盘设备创建为物理卷设备
pvcreate /dev/sdb

＃　将指定的多个磁盘设备创建为物理卷设备
pvcreate /dev/sdc{1,2,3,4}
```

###### vgextend

＊　vgextend 命令来自英文词组 volume group extend 的缩写，其功能是扩展卷组设备。LVM（逻辑卷管理器）技术具有灵活调整卷组与逻辑卷的特点，可以在创建卷组时规定物理卷的数量，亦可在后期使用 vgextend 命令进行扩展

```shell
# 将指定的物理卷加入卷组设备
vgextend VG01 /dev/sdb
```

###### pvresize

* pvresize 命令来自英文词组 physical volume resize 的缩写，其功能是调整 LVM 中物理卷的容量大小。pvresize 命令可以调整已在卷组中的物理卷的容量大小，一般在物理卷设备扩容或缩容前进行此操作，并在提前告知操作系统和 LVM 新的物理卷大小，若要缩小物理卷的容量，则不得低于已使用的容量

```shell
# 同步物理卷的容量为最新大小
pvresize /dev/sda2

# 调整物理卷的容量大小为 20GB（需二次确认）
pvresize --setphysicalvolumesize 20G /dev/sda2

# 以测试模式运行，同步物理卷的容量为最新大小
pvresize -t /dev/sda2
```

###### vgdisplay

* vgdisplay 命令来自英文词组 volume group display 的缩写，其功能是显示 VG（卷组）信息。细心负责的运维人员在创建卷组后都会使用 vgdisplay 命令再次确认，检查 PE 大小、容量、名称等信息是否正确，在一切稳妥后再进行下一步操作

```shell
# 查看系统中全部的卷组信息
vgdisplay

# 查看系统中指定名称的卷组信息
vgdisplay VG01

# 仅查看系统中指定名称的卷组的属性信息
vgdisplay -A VG01

# 仅查看系统中指定名称的卷组的短格式属性信息
vgdisplay -s VG01
```

###### vgcreat

* vgcreate 命令的功能是创建卷组设备。LVM 逻辑卷管理器技术由物理卷、卷组和逻辑卷组成，其中 vgcreate 命令属于 LVM 创建工作的第二个环节——创建卷组设备。卷组，顾名思义是将多个物理卷组成一个整体，由于屏蔽了底层物理设备细节，用户可
  在创建逻辑卷后无须再考虑具体的硬件设备信息
* 语法格式：vgcreate 参数 卷组名 设备名

```shell
# 使用两块硬盘，创建出一块指定名称的卷组设备
vgcreate VG01 /dev/sdb /dev/sdc
```

###### lvextend

* lvextend 命令来自英文词组 logical volume extend 的缩写，其功能是扩展逻辑卷设备。LVM（逻辑卷管理器）技术具有灵活调整卷组与逻辑卷的特点，逻辑卷设备容量可以在创建时规定，亦可在后期根据业务需求进行动态扩展或缩小

```shell
# 将指定的逻辑卷设备扩展至 290MB
lvextend -L 290M /dev/storage/vo
```

#### RAID设备

###### mdadm

* mdadm 命令来自英文词组 multiple devices admin 的缩写，其功能是管理 RAID 设备。作为 Linux 系统下软 RAID 设备的管理神器，mdadm 命令可以进行创建、调整、监控、删除等全套管理操作

```shell
# 使用 4 块硬盘设备创建一个指定名称且级别为 RAID 10 的磁盘阵列组
mdadm -Cv /dev/md0 -n 4 -l 10 /dev/sdb /dev/sdc /dev/sdd /dev/sde

# 查看指定 RAID 设备的简要信息
mdadm -Q /dev/md0

# 查看指定 RAID 设备的详细信息
mdadm -D /dev/md0

# 将指定的硬盘从 RAID 设备中停止
mdadm /dev/md0 -f /dev/sdb

# 将指定的硬盘添加至 RAID 设备中
mdadm /dev/md0 -a /dev/sdb

# 彻底停用一个 RAID 设备
mdadm --stop /dev/md0
```

### 资源命令

#### 磁盘

###### fstrim

* fstrim 命令来自英文词组 filesystem trim 的缩写，其功能是回收文件系统中未使用的块资源。fstrim 命令对固态硬盘和精简配置的存储设备意义较大，有提高驱动器读写效率，延长使用寿命的作用，当然设备一定要支持该命令才行

```shell
# 回收当前系统上所有已挂载的文件系统的未使用空间
fstrim -a

# 回收当前系统上所有已挂载的文件系统的未使用空间，并显示详细的过程
fstrim -a -v

# 



```









---

### 进程命令

#### 查看

###### ps

* ps 命令来自英文单词 process 的缩写，中文译为“进程”，其功能是显示当前系统的进程状态。使用 ps 命令可以查看到进程的所有信息，例如进程的号码、发起者、系统资源（处理器与内存）使用占比、运行状态等。ps 命令可帮助我们及时发现哪些进程出现“僵死”或“不可中断”等异常情况
* ps 命令经常会与 kill 命令搭配使用，以中断和删除不必要的服务进程，避免服务器的资源浪费

> 参数
>
> * a（`列出带有终端的所有用户的进程`）
> * x（`列出当前用户的所有进程，包括没有终端的进程`）
> * u（**面向用户友好的显示风格**）
> * e（==列出所有进程==）
> * u（`列出某个用户关联的所有进程`）
> * f（**显示完整格式的进程列表**）
>

```shell
# 显示系统中全部的进程信息，含详细信息
ps aux

# 结合输出重定向，将当前进程信息保留备份至指定文件
ps aux > File.txt

# 结合管道操作符，将当前系统运行状态中指定的进程信息过滤出来
ps -ef | grep ssh

# 结合管道操作符，将当前系统运行状态中指定用户的进程信息过滤出来
ps -u root

# 结合管道操作符与 sort 命令，依据处理器使用量（第三列）情况降序排序
ps aux | sort -rnk 3

# 结合管道操作符与 sort 命令，依据内存使用量（第四列）情况降序排序
ps aux | sort -rnk 4

# 查看指定进程
ps -aux | grep "mysql"

# 查看父子进程之间的关系
ps -ef | grep "mysql"
```

###### runlevel

```shell
# 查看运行级别
runlevel
systemctl get-default

# 修改当前运行级别  multi-user.target（运行级别3） graphical.target（运行级别5）
# TARGET 取 multi-user 或者 graphical
systemctl set-default TARGET.target
```

###### pstree

```shell
# 显示进程id
pstree -p

# 显示进程所属用户
pstree -u
```

###### top

* top 命令的功能是实时显示系统运行状态，包含处理器、内存、服务、进程等重要资产信息。运维工程师们常常会把 top 命令比作“加强版的 Windows 任务管理器”，因为除了能看到常规的服务进程信息之外，还能够对处理器和内存的负载情况一目了然，实时感知系统全局
  的运行状态。top 命令非常适合作为接手服务器后执行的第一条命令

> 按键
>
> * ==P==:以 CPU 使用率排序，默认就是此项
> * ==M==:以内存的使用率排序
> * ==N==:以 PID 排序
> * `q`:退出 to

```shell
# 以默认格式显示系统运行信息
top

# 以默认格式显示系统运行信息，但提供完整的进程路径及名称
top -c

# 以批处理模式显示程序信息
top -b

# 设定总显示次数为 5 次，随后自动退出命令
top -n 5

# 每隔3s跟新系统进程状态
top -d 3 

# 不显示任何闲置或僵死进程
top -i

# 根据进程id监控指定进程
top -p 2534
```

###### lsof

* lsof 命令来自英文词组 list opened files 的缩写，其功能是查看文件的进程信息。由于 Linux系统中的一切都是文件，因此使用 lsof 命令查看进程打开的文件，或是查看文件的进程信息，都能帮助用户很好地了解相关服务的运行状态，是一个不错的系统监视工具

```shell
# 查看当前系统中全部文件与进程的对应信息
lsof

# 显示指定目录中被调用的文件信息
lsof +d /root

# 递归显示指定目录中全部被调用的文件信息
lsof +D /root
```



#### 设置

###### systemctl

* systemctl 命令来自英文词组 system control 的缩写，其功能是管理系统服务。从 RHEL 7/CentOS 7 版本起，初始化进程服务 init 被替代为 systemd 服务，systemd 初始化进程服务的管理是通过 systemctl 命令完成的，该命令涵盖了 service、chkconfig、init、setup 等多个命令的大部分功能

```shell
# 查看服务开机启动状态
systemctl list-unit-files

# 关掉服务的自动启动
systemctl disable firewalld
sudo ufw disable

# 开启服务的自动启动
systemctl enable firewalld
sudo ufw enable

# 查看服务状态
systemctl status firewalld
sudo ufw status

# 临时关闭服务
systemctl stop firewalld
sudo systemctl stop ufw
```

###### kill

* kill 命令的功能是杀死（结束）进程。Linux 系统中如需结束某个进程，既可以使用如service 或 systemctl 这样的管理命令来结束服务，也可以使用 kill 命令直接结束进程信息。如使用 kill 命令后进程并没有结束，则可以使用信号 9 进行强制杀死动作

```shell
# 结束某个指定的进程（数字为对应的 PID 值）
kill 518

# 强制结束某个指定的进程（数字为对应的 PID 值）
kill -9 518

# 根据进程id停止进程
kill -9 5425

# 根据进程名称停止进程
killall mysql
```

###### exit

* exit 命令的功能是退出终端。在终端或 Shell 脚本中执行 exit 命令默认会直接退出终端，亦可在命令后添加状态值参数，这样退出后可方便后续脚本判断本次执行结果是否成功（例如执行 echo $?命令）

```shell
# 退出当前 Shell 终端
exit

# 退出当前 SSH 连接终端
exit


```

###### xargs

* xargs 命令来自英文词组 extended arguments 的缩写，用作给其他命令传递参数的过滤器。xargs 命令能够处理从标准输入或管道符输入的数据，并将其转换成命令参数，也可以将单行或多行输入的文本转换成其他格式。xargs 命令默认接收的信息中，空格是默认定界符，所以可以接收包含换行和空白的内容

```shell
# 默认以空格为定界符，以多行形式输出文件内容，每行显示 3 段内容值
cat File.cfg | xargs -n 3

# 指定字符 X 为定界符，默认以单行的形式输出字符串内容
echo "FirstXSecondXThirdXFourthXFifth" | xargs -dX

# 指定字符 X 为定界符，以多行形式输出文本内容，每行显示两段内容值
echo "FirstXSecondXThirdXFourthXFifth" | xargs -dX -n 2

# 设定每一次输出信息时，都需要用户手动确认后再显示到终端界面
echo "FirstXSecondXThirdXFourthXFifth" | xargs -dX -n 2 -p

# 由 xargs 调用要执行的命令，并将结果输出到终端界面
ls | xargs -t -I{} echo {}
```





#### 防火墙

###### iptables-save

* iptables-save 命令的功能是保存防火墙策略规则。由于 iptables 与 firewalld 防火墙配置工具的策略默认都是当前生效而重启后失效，因此均需要执行对应的命令进行保存，让已有的防火墙策略在服务器重启后依然可以奏效

```shell
# 保存防火墙策略规则
iptables-save

# 保存防火墙策略规则及数据包计数器信息
iptables-save -c

# 将当前防火墙策略规则信息输出重定向到文件
iptables-save > File.bak

# 仅保存防火墙策略中指定的表单内容
iptables-save -t filter
```

###### iptables

* iptables 是一个用于管理防火墙策略的命令，同时也是一个基于内核级别的防火墙服务，用户可以基于它对数据包进行过滤操作，拒绝掉危险的外部请求流量，保护内网的安全。iptables 命令默认仅支持 IPv4 协议，如需支持 IPv6 协议，则需使用 ip6tables 命令

```shell
# 显示当前防火墙策略中全部的过滤表信息
iptables -L

# 显示当前防火墙策略中指定的 NAT 表信息
iptables -L -t nat

# 禁止指定的远程主机访问本地全部的服务（通通禁止）
iptables -I INPUT -s 192.168.10.10 -j DROP

# 禁止指定的远程主机访问本地的某个端口，但允许访问其余端口
iptables -I INPUT -s 192.168.10.10 -p tcp --dport 22 -j DROP
```

###### firewall-cmd

* firewall-cmd 命令的功能是管理防火墙策略，是 firewalld 服务的配置工具。使用 firewall-cmd命令修改的防火墙策略会立即生效，但重启后失效，因此在使用时推荐加上 permanent 参数

```shell
# 查看当前防火墙状态
firewall-cmd --state

# 查看防火墙当前放行端口号列表
firewall-cmd --zone=public --list-ports

# 重新加载防火墙策略，立即生效
firewall-cmd --reload

# 查看当前防火墙默认使用的区域名称
firewall-cmd --get-default-zone

# 设置当前防火墙默认使用的区域名称
firewall-cmd --set-default-zone=dmz

# 开启紧急模式，随后关闭
firewall-cmd --panic-on
firewall-cmd --panic-off

# 设置 8080-8081 为防火墙允许放行的端口号
firewall-cmd --zone=public --add-port=8080-8081/tcp

# 查看防火墙当前放行端口号列表
firewall-cmd --zone=public --list-ports

# 查询指定服务的流量是否被防火墙允许放行
firewall-cmd --zone=public --query-service=ssh


```







### 其他命令

#### 查看

###### free

* free 命令的功能是显示系统内存使用量情况，包含物理内存和交换内存的总量、使用量、空闲量情况

```shell
# 以默认的容量单位显示内存使用量信息
free

# 以 MB 为单位显示内存使用量信息
free -m

# 以易读的单位显示内存使用量信息
free -h

# 以易读的单位显示内存使用量信息，每隔 10s 刷新一次
free -hs 10
```

#### 加密

###### hash

* hash 命令来自英文词组 hash algorithm 的缩写，中文译为“哈希算法、杂凑算法”，其功能是管理命令运行时查询的哈希表。hash 命令可以显示与删除命令运行时系统查询的哈希表信息，如果不加任何参数，则会默认输出路径列表的信息，这个列表会包含先前 hash 命令调用找到的 Shell 环境中命令的路径名

```shell
# 显示哈希表中的命令
hash -l

# 删除哈希表中的命令
hash -r

# 向哈希表中添加命令
hash -p /usr/sbin/adduser myadduser

# 在哈希表中清除记录
hash -d
```

#### 监测

###### fio

* fio 命令来自英文词组 flexible I/O tester 的缩写，其功能是对磁盘进行压力测试。硬盘 I/O吞吐率是其性能的重要指标之一，运维人员可以使用 fio 命令对其进行测试，测试又可以细分为顺序读写和随机读写两大类

```shell
# 进行随机读取测试
fio -filename=File -direct=1 -iodepth 1 -thread -rw=read -ioengine= psync -bs=16k -size=10G -numjobs=10 -runtime=100 -group_reporting -name=mytest

# 进行随机写入测试
fio -filename=File -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=16k -size=200G -numjobs=30 -runtime=1000 -group_reporting -name=mytest

# 进行顺序写入测试
fio -filename=File -direct=1 -iodepth 1 -thread -rw=write -ioengine=psync -bs=16k -size=200G -numjobs=30 -runtime=1000 -group_reporting -name=mytest
```

###### tcpdump

* tcpdump 命令的功能是监听网络流量，是一款数据嗅探工具，在 Linux 系统中常用来抓取数据包，能够记录所有经过服务器的数据包信息。tcpdump 命令需要以管理员身份执行

```shell
# 监听指定网络接口的数据包
tcpdump -i ens160

# 监听指定主机的数据包（主机名）
tcpdump host linuxcool.com

# 监听指定主机的数据包（IP 地址）
tcpdump host 192.168.10.10

# 监听指定端口号的数据包，并以文本形式展示
tcpdump -i any port 80 -A
```

###### fsck

* fsck 命令来自英文词组 filesystem check 的缩写，其功能是检查与修复文件系统。若系统有过突然断电或磁盘异常的情况，建议使用 fsck 命令对文件系统进行检查与修复，以防数据丢失

```shell
# 检查文件系统是否有损坏
fsck /dev/sdb

# 强制检查文件系统的损坏情况
fsck -f /dev/sdb
```



###### bc

bc 命令来自英文词组 binary calculator 的缩写，中文译为“二进制计算器”，其功能是进
行数字计算。bash 解释器仅能进行整数计算，而不支持浮点数计算，因此有时要用到 bc 命令
进行高精度的数字计算工作

```shell
# 计算得出指定的浮点数乘法结果
bc
1.2345*3

# 设定计算精度为小数点后 3 位，取浮点数除法结果
bc
scale=3
3/8


# 分别计算整数的平方与平方根结果
bc

10^10

sqrt（100）
```











### 管理命令

#### 证书

###### keytool

* keytool 命令的功能是管理密钥和证书文件。密钥就是用于加解密的文件或字符串，可以使用 keytool 命令进行生成、导入和删除等操作。不同的程序需要的密钥格式不尽相同，需要留意具体的格式

```shell
# 生成一个指定名称的证书文件，加密类型为 RSA，有效期365 天
keytool -genkey -alias tomcat -keyalg RSA -keystore /etc/

# 导入一个指定名称的证书文件，定义别名名称
 keytool -import -keystore cacerts -storepass 666666 -keypass 888888 -alias linuxcool -file /etc/tomcat.keystore

# 删除一个指定名称的证书
keytool -delete -alias linuxcool -keystore cacerts -storepass 666666
```

###### ssh-keyge

ssh-keygen 命令来自英文词组 SSH key generate 的缩写，其功能是生成 SSH 密钥文件。
ssh-keygen 命令能够对 SSH 密钥文件进行生成、管理、转换等工作，支持 RSA 和 DSA 两种
密钥格式



```shell
# 创建一个 SSH 密钥文件
ssh-keygen


# 



```

###### ssh-keyscan

ssh-keyscan 命令的功能是收集主机的 SSH 公钥信息。Linux 系统管理员通常会先用
ssh-keygen 命令生成 SSH 密钥文件，随后用 ssh-copy-id 命令传送公钥文件到对方主机，而
ssh-keyscan 命令的作用则是收集主机上的公钥信息，创建和验证 sshd 服务程序的
ssh_known_hosts 文件。ssh-keyscan 命令仅支持 SSHv1，在 SSHv2 中无法使用



```shell
# 收集主机 SSH 公钥，并输出调试信息
ssh-keyscan -v 192.168.10.10

# 显示指定主机上的 RSA 密钥信息
ssh-keyscan 192.168.10.10

# 显示指定主机上的 DSA 密钥信息
ssh-keyscan -t dsa 192.168.10.10

# 显示调试信息
ssh-keyscan -v




```















#### 蓝牙

###### rfkill

* rfkill 命令来自英文词组 radio frequency kill 的缩写，其功能是管理系统中的蓝牙和 Wi-Fi
  设备，是一个内核级别的管理工具

```shell
# 显示系统中已有的 Wi-Fi 和蓝牙设备信息
rfkill list

# 关闭指定编码的设备
rfkill block 0

# 打开指定编码的设备
rfkill unblock 0




```

#### 定时任务

###### crontab

* crontab 命令来自英文词组 cron table 的缩写，其功能是管理定时计划任务。定时计划任务，顾名思义就是计划好的任务，到了时间就会自动执行。在 Linux 系统中，crond 是一个定时计划任务服务，用户只要能够按照正确的格式（分、时、日、月、星期、命令）写入配置文件，那么就会按照预定的周期时间自动执行，而 crontab 命令则是用于配置定时计划任务的工具名称

```shell
# 管理当前用户的计划任务
crontab -e
 
# 管理指定用户的计划任务
crontab -e -u linuxprobe

# 查看当前用户的已有计划任务列表
crontab -l
```



###### export

export 命令的功能是将变量提升成环境变量，亦可将 Shell 函数输出为环境变量。通常个
人创建出的变量仅能在自己账户下使用，其他人是无法看到的。若想让每个人都能看到并有
权利去使用变量值，则需要使用 export 命令进行提升操作



```shell
# 列出当前系统中所有的环境变量信息
export -p

# 将指定变量提升成环境变量
export MYENV

# 定义一个变量并提升成环境变量
export MYENV=www.linuxcool.com
```



###### ftp

ftp 命令来自英文词组 file transfer protocol（FTP）的缩写，是一个文件传输协议客户端。
FTP 是当前最常用的文件传输协议之一，而 ftp 命令也是最常用的 FTP 协议客户端，它能够
用于在本地主机和远程主机之间上传和下载文件，实现两端的通信。
在登录时匿名 FTP 服务器，使用 anonymous 作为用户名，使用任意的电子邮件作为密码。
通常，用户只能从匿名 FTP 服务器下载文件，而能上传文件。另外，FTP 使用明文传送用户
的认证信息，很容易被局域网内的嗅探软件截获，所以使用 ftp 命令时要格外注意

```shell
# 使用匿名模式，连接到指定的远程 FTP 服务器
ftp 192.168.10.10

# 从 FTP 服务器中下载指定的文件到本地目录
ftp> get File.txt

# 从本地目录上传文件到 FTP 服务器中
ftp> put File.txt

# 查看 FTP 服务的帮助信息
ftp> help

# 查看 FTP 服务器中的文件列表
ftp> ls

# 删除 FTP 服务器中的指定文件
ftp> delete File.txt

# 在 FTP 服务器中创建一个远程目录
ftp> mkdir linux

# 退出连接
ftp> quit

```



###### init

init 命令来自英文单词 initialize 的缩写，其功能是切换系统运行级别。init 命令是 Linux
系统中的进程初始化工具，是一切服务程序的父进程，它的进程号永远为 1。管理员可以使用
init 命令对系统运行级别进行自由切换，亦可进行重启、关机等操作

```shell
# 关闭服务器
init 0

# 切换单用户模式
init 1

# 切换多用户模式
init 2

# 切换完全多用户模式（常见的文字界面级别）
init 3

# 切换图形界面模式（常见的图形界面级别）
init 5

# 重启服务器
init 6




```



###### whereis

whereis 命令的功能是显示命令及相关文件的路径位置信息，可用于找到命令（二进制程
序）、命令源代码、man 帮助手册等相关文件的路径位置信息，帮助我们更好地管理这些文件。
有别于 find 命令进行的全盘搜索，whereis 命令的查找速度非常快，因为它不是在磁盘中
乱找，而是在指定数据库中查询，该数据库是 Linux 系统自动创建的，包含本地所有文件的
信息，每天自动更新一次。但也正因为这样，whereis 命令的搜索结果会不及时，比如刚添加
的文件可能搜不到，原因就是该数据库文件还没有更新，管理人员需手动执行 updatedb 命令
进行更新



```shell
# 查找指定命令程序及相关文件所在的位置
whereis poweroff

# 仅查找指定命令程序文件所在的位置
whereis -b poweroff

# 仅查找指定命令的帮助文件所在的位置
whereis -m poweroff


```



###### tracepath

tracepath 命令的功能是追踪数据包的路由信息。tracepath 命令能够追踪并显示数据包到
达目的主机所经过的路由信息，以及对应的 MTU 值



```shell
# 追踪到达域名的主机路由信息
tracepath www.linuxcool.com

# 追踪到达域名的主机路由信息，同时显示 IP 地址与主机名
tracepath -b www.linuxcool.com

# 设置追踪数据包路由的最大 TTL 值为 20，并追踪到达域名的主机路由信息
tracepath -m 20 www.linuxcool.com




```



###### alias

alias 命令来自英文单词 alias，中文译为“别名”，其功能是设置命令别名信息。我们可以
使用 alias 将一些较长的命令进行简写，往往几十个字符的命令会变成几个字母，从而大大提
高我们的工作效率。
必须使用单引号将原来的命令引起来，防止特殊字符导致错误。并且 alias 命令的作用只
局限于该次登入的操作，若要每次登入都能使用这些命令别名，则可将相应的 alias 命令存放
到 bash 的初始化文件/etc/bashrc 中



```shell
# 查看系统中已有的命令别名信息
alias -p

# 新增一个命令别名（lc），其作用是查看当前目录下文件列表及权限等信息
alias lc='ls -al'

# 


```



###### blktrace

blktrace 命令来自英文词组 block trace 的缩写，其功能是分析磁盘 I/O 负载情况。在查
看 Linux 系统磁盘的负载情况时，我们一般会使用 iostat 监控工具，其中很重要的参数就
是 await。await 表示单个 I/O 所需的平均时间，但它同时也包含了 I/O 调度器所消耗的时
间和硬件所消耗的时间，所以不能作为硬件性能的指标。
那么，如何才能分辨一个 I/O 从下发到返回的整个时间内，是硬件耗时多还是在 I/O 调度
耗时多呢？如何查看 I/O 在各个时间段所消耗的时间呢？blktrace 命令在这种场合就能派上用
场了，因为它能记录 I/O 所经历的各个步骤，从中可以分析是 IO 调度器慢还是硬件响应慢，
以及它们各自所用的时间



```shell
# 分析指定磁盘的 I/O 情况
blktrace -d /dev/sda

# 设置运行的时间为 30 秒，分析指定磁盘的 I/O 情况
blktrace -w 30 -d /dev/sda

# 分析指定磁盘的 I/O 情况，并指定输出文件的名称
blktrace -d /dev/sda -o File


```



###### partprobe

partprobe 命令来自英文词组 partition probe 的缩写，其功能是重读分区表信息。该命令可
将磁盘分区表变化信息通知给系统内核，请求操作系统重新加载分区表。有时我们在创建或
删除分区设备后，系统并不会立即生效，这时就需要使用 partprobe 命令在不重启系统的情况
下重新读取分区表信息，使新设备信息与系统同步



```shell
# 重读系统中全部设备的分区表信息
partprobe

# 重读系统中指定设备的分区表信息
partprobe /dev/sda

# 查看命令的帮助信息
partprobe -h





```



###### lsattr

lsattr 命令来自英文词组 list attribute 的缩写，其功能是显示文件的隐藏属性。隐藏属性也
叫隐藏权限，顾名思义就是用 chattr 命令添加在文件上的隐藏权限属性。这些属性信息用常
规的 ls 命令无法查看，需要使用 lsattr 命令查看

```shell
# 查看指定文件的隐藏属
lsattr File.cfg

# 仅查看指定目录本身的隐藏属性
lsattr -d /root

# 查看指定目录中全部文件的隐藏属性
lsattr -a /root



```



###### timedatectl

timedatectl 命令来自英文词组 time date control 的缩写，其功能是设置系统时间与日期。
与使用 date 命令设置日期时间不同，使用 timedatectl 命令设置过的日期时间信息将被写入系
统配置文件，从而立即且长期有效，不会随系统重启而失效。该命令还能用来查看系统时间
与日期，一站式搞定系统时间。



```shell
# 查看当前系统中的时区、日期、时间等信息
timedatectl status

# 关闭 NTP 时间服务器同步功能
timedatectl set-ntp false

# 设置系统日期
timedatectl set-time 2024-05-18

# 设置系统时间
timedatectl set-time 20:18

# 查看可选时区
timedatectl list-timezones

# 设置系统时区
timedatectl set-timezone "Asia/Shanghai"



```



###### hexdump

hexdump 命令来自英文词组 hexadecimal dump 的缩写，其功能是以多种进制格式查看文
件内容。hexdump 命令是 Linux 系统中一款好用的文件内容查看工具，可以将文件内容转换
成 ASCII、二进制、八进制、十进制、十六进制格式进行查看，满足各种需求



```shell
# 以十六进制格式查看指定文件的内容
hexdump File.cfg

# 以十六进制和 ASCII 格式查看指定文件的内容
hexdump -C File.cfg

# 以十进制格式查看指定文件的内容
hexdump -d File.cfg






```



###### stat

stat 命令来自英文单词 status 的缩写，其功能是显示文件的状态信息。在 Linux 系统中，
每个文件都有 3 个“历史时间”——最后访问时间（ATIME）、最后修改时间（MTIME）、最
后更改时间（CTIME），用户可以使用 stat 命令查看到它们，进而判别有没有其他人修改过文
件内容。
使用 touch 命令可以轻易修改文件的 ATIME 和 MTIME，因此请勿单纯以文件历史时间
作为判别系统有无被他人入侵的唯一标准。



```shell
# 查看指定文件的状态信息（含 ATIME、MTIME 与 CTIME）
stat

# 仅查看指定文件的文件系统信息
stat -f File.cfg

# 以简洁的方式查看指定文件的状态信息
stat -t File.cfg




```



###### gpasswd

gpasswd 命令来自英文词组 group password 的缩写，其功能是设置管理用户组。用户可以
使用 gpasswd 命令对用户组进行充分的管理，例如设置/删除密码、添加/删除组成员、设置组
管理员/普通成员等，提高日常工作中对用户组的管理效率。



```shell
# 将指定用户加入到 root 管理员用户组

gpasswd -a linuxprobe root

# 将指定用户从 root 管理员用户组中删除

gpasswd -d root linuxprobe

# 为指定用户组设置管理密码
gpasswd root

# 删除指定用户组中的管理密码
gpasswd -r root



```



###### rsync

rsync 命令来自英文词组 remote sync 的缩写，其功能是远程数据同步。rsync 命令能够基
于网络（包含局域网和互联网）快速地实现多台主机间的文件同步工作。与 scp 或 ftp 命令会
发送完整的文件不同，rsync 有独立的文件内容差异算法，会在传送前对两个文件进行比较，
只传送两者内容间的差异部分，因此速度更快



```shell
# 将本地目录（/Dir）与远程目录（192.168.10.10:/Dir）相关联，保持文件同步
rsync -r /Dir 192.168.10.10:/Dir

# 将远程目录（192.168.10.10:/Dir）与本地目录（/Dir）相关联，保持文件同步
rsync -r 192.168.10.10:Dir /Dir

# 关联两个本地的目录，保持文件同步
rsync -r /Dir1 /Dir2

# 列出本地指定目录内的文件列表
rsync /Dir2/

# 列出远程指定目录内的文件列表
rsync 192.168.10.10:/Dir/
```





###### last

last 命令的功能是显示用户历史登录情况。通过查看系统记录的日志文件内容，可使管理
员获知谁曾经或者试图连接过服务器。
通过读取系统登录历史日志文件（/var/log/wtmp）并按照用户名、登录终端、来源终端、
时间等信息进行划分，可让用户对系统历史登录情况一目了然



```shell
# 显示近期用户或终端的历史登录情况
last

# 仅显示最近 3 条历史登录情况，并不显示来源终端信息
last -n 3 -R

# 显示系统的开关机历史信息，并将来源终端放到最后
last -x -a



```



###### md5sum

md5sum 命令来自英文词组 MD5 summation 的缩写，其功能是计算文件内容的 MD5
值，进而比较两个文件是否相同。MD5 值是一个 128 位的二进制数据，转换成十六进制
则是 32 位。
用户可以通过此命令对文件内容进行汇总并计算出一个 MD5 值，如果有某两个文件的
MD5 值完全相同，则代表两个文件内容完全相同。文件名称不对计算结果产生影响。

```shell
# 生成文件 MD5 值
md5sum File.cfg

# 以文本模式读取文件内容，并生成 MD5 值
md5sum -t File.cfg

# 以二进制模式读取文件内容，并生成 MD5 值
md5sum -b File.cfg

```



###### mail

mail 命令的功能是发送和接收邮件，是 Linux 系统中重要的电子邮件管理工具，自RHEL 8 /
CentOS 8 系统起，该命令正式改名为 mailx，而 mail 则作为软链接文件保留



```shell
# 向指定的邮箱发送信件 ，以单个句号（.）结束邮件
mail root@linuxprobe.com

# 查看当前用户身份下的邮件信息
mail






```



###### showmount

showmount 命令来自英文词组 show mounted disk 的缩写，其功能是显示 NFS 服务器的共
享信息。NFS 是一款广泛使用的 Linux 系统文件共享服务，客户通常仅需先使用 showmount
命令查看 NFS 服务器的共享设备信息，随后使用 mount 命令远程挂载到本地即可使用，无须
密码验证

```shell
# 获取已经被客户端加载的 NFS 共享目录
showmount -d 192.168.10.10

# 获取 NFS 服务器的全部共享目录
showmount -e 192.168.10.10




```





###### dnf

dnf 命令来自英文词组 dandified yum 的缩写，是新一代的软件包管理器，其功能是安装、
更新、卸载 Linux 系统中的软件。dnf 最初应用于 Fedora 18 系统中，旨在解决 yum 命令的诸
多瓶颈，例如占用大量内存、软件依赖关系臃肿、运行速度缓慢等。
dnf 与 yum 命令的执行格式高度相同，只需要将日常软件包管理操作中的 yum 替换成 dnf
命令即可。



```shell
# 安装指定的软件包
dnf install httpd

# 安装指定的软件包，且无须二次确认
dnf install httpd -y

# 更新指定的软件包
dnf update httpd

# 重新安装指定软件包
dnf reinstall httpd

# 卸载指定的软件包
dnf remove httpd

# 查询软件仓库中已有软件包列表
dnf list

# 更新系统中所有的软件包至最新版


```



###### nmtui

nmtui 命令来自英文词组 network manager tui 的缩写，其功能是管理网卡配置参数。用户
可以使用 nmtui 命令在终端下调出类图形界面，然后使用方向键和 Enter 键即可进行控制，因
此对于不会使用 nmcli 命令的新手管理员来讲十分友好。



```shell
# 进入网卡参数配置界面
nmtui

# 




```





###### iscsiadm

iscsiadm命令来自英文词组iSCSI administration的缩写，是最常用的iSCSI服务管理工具。
iscsiadm 是一个命令行工具，能够发现、登录、卸载远程 iSCSI 目标，还能管理 open-iscsi 数
据库。iSCSI 服务的配置过程较复杂，建议参考《Linux 就该这么学（第 2 版）》的 17.3 节。
有些时候，服务器可能未安装 iSCSI 服务应用程序，此时需要先安装后使用，如执行 dnf install
targetcli -y 命令安装。



```shell
# 发现远程可用的 iSCSI 服务器节点
iscsiadm -m discovery -t st -p 192.168.10.10

# 登录远程可用的 iSCSI 服务器节点
iscsiadm -m node -T iqn.2003-01.org.linux-iscsi.linuxprobe.

# 卸载本地已挂载的指定 iSCSI 存储设备
iscsiadm -m node -T iqn.2003-01.org.linux-iscsi.linuxprobe.
x8664:sn.745b21d6cad5 -u

# 卸载本地已挂载的全部 iSCSI 存储设备
iscsiadm -m node --logoutall=all

# 

```





###### nc

nc 命令来自英文词组 net cat 的缩写，其功能是扫描与连接指定端口。nc 命令是一个功能
丰富的网络实用工具，被誉为网络界的“瑞士军刀”，短小精悍，功能实用。它支持 TCP 和
UDP 协议，能够基于命令行在网络上读取和写入数据，连接与扫描指定端口号，为用户提供
无限的潜在用途。



```shell
# 扫描指定主机的 80 端口（默认为 TCP）
nc -nvv 192.168.10.10 80

# 扫描指定主机的 1～1000 端口，指定为 UDP
nc -u -z -w2 192.168.10.10 1-1000

# 扫描指定主机的 1~100 端口，并显示执行过程
nc -v -z -w2 192.168.10.10 1-100




```





###### service

service 命令的功能是管理系统服务，是早期红帽公司发行的 Linux 系统中最常见的命令
之一，主要用于 RHEL 7/CentOS 7 版本以前的系统，能够启动、停止、重启或关闭指定服务
程序，亦能查看服务的运行状态信息。

```shell
# 查看系统中所有服务的状态
service --status-all

# 查看指定服务的状态
service sshd status

# 启动指定的服务程序
service sshd start

# 关闭指定的服务程序
service sshd stop

# 重启指定的服务程序
service sshd restart





```



###### mkpasswd

mkpasswd 命令来自英文词组 make password 的缩写，其功能是生成用户的新密码。
mkpasswd 命令可以生成一个适用于用户的随机的新密码，管理员可以指定随机密码的长度及
所含字符的规则。有经验的用户可以结合管道符将新生成的密码直接作用于用户，一条命令
即可设置好新密码。
每次生成的随机密码均不同，请在正式设置用户密码前保存好，不要忘记哦！



```shell
# 生成出一个长度为 20 字符的新密码
mkpasswd -l 20

# 生成出一个含 3 位数字的新密码
mkpasswd -d 3

# 生成出一个长度为 20 字符、含 5 位大写字母的新密码，并自动为指定用户进行新密码设定
mkpasswd -C 5 -l 10 | passwd --stdin linuxcool





```



###### uptime

uptime 命令的功能是查看系统负载，是 Linux 系统中最常用的命令之一。uptime 命令能
够显示系统已经运行了多长时间、当前登入用户的数量，以及过去 1 分钟、5 分钟、15 分钟
内的负载信息。该命令的用法也十分简单，一般不需要加参数，直接输入 uptime 即可。



```shell
# 查看当前系统负载及相关信息
uptime

# 以更易读的形式显示系统的已运行时间
uptime -p


# 显示本次系统的开机时间
uptime -s


```



###### nmap

nmap 命令来自英文词组 network mapper 的缩写，中文译为“网络映射器”。nmap 是一款
开放源代码的网络探测和安全审计工具，能够快速扫描互联网、局域网或单一主机上的开放
信息，基于原始 IP 数据包自动分析网络中有哪些主机、主机提供何种服务、服务程序的版本
是什么，从而为日常维护和安全审计提供数据支撑。
除了可帮助管理员了解整个网络情况外，nmap 还能获取目标主机的更深入的信息，例如
反向域名、操作系统与设备的种类及类型、MAC 网卡地址信息等。

```shell
# 扫描目标主机并跟踪路由信息
nmap --traceroute www.linuxcool.com

# 扫描目标主机上的特定端口号信息
nmap -p80,443 www.linuxcool.com

# 扫描目标主机上的指定端口号段信息
nmap -p1-10000 www.linuxcool.com

# 使用高级模式扫描目标主机
nmap -A www.linuxcool.com
```



###### ntpdate

ntpdate 命令来自英文词组 NTP date 的拼写，其功能是设置日期和时间。ntpdate 命令能够
基于 NTP 设置 Linux 系统的本地日期和时间。通过利用 NTP 服务的时钟过滤器来选择最优方
案，可大大提高时间的可靠性和精度，让系统时间总是准确无误。



```shell
# 调整日期时钟
ntpdate -b

# 向指定的 NTP 服务器同步时间
ntpdate ntp.aliyun.com

# 仅向指定的 NTP 服务器查询时间，但不进行同步设置
ntpdate -q ntp.aliyun.com




```



###### cal

cal 命令来自英文单词 calendar 的缩写，中文译为“日历”，其功能是显示系统月历与日
期信息。该命令简单好用，无须过多介绍，想好需求后参考常用参数即可使用



```shell
# 显示当前月份及对应日期
cal

# 显示指定的月历信息，如 2025 年 2 月
cal 2 2025

# 显示最近 3 个月的日历（上个月、当前月、下个月）
cal -3




```





###### at

at 命令的功能是设置一次性定时计划任务，是 Linux 系统中常用的计划任务工具之一，
会以 atd 守护进程的形式在后台运行。相较于 crond 周期性计划任务服务程序，at 命令的特点
就是计划任务具有一次性特征，即一旦设置的计划任务被执行，该任务就会从任务列表库中
删除，因此常被用于仅需执行一次的工作。



```shell
# 查看系统中的等待任务
at -l

# 删除系统中指定编码为 1 的计划任务
 at -r 1
 
# 使用计划任务立即执行某指定脚本文件
at -f File.sh now

# 使用计划任务设置 25 分钟后执行某个指定的脚本文件
at -f File.sh now+25 min

# 使用计划任务设置今天的 10:11 准时执行某个指定的脚本文件
at -f File.sh 10:11

# 使用计划任务设置在 2024 年 5 月 18 日准时执行某个脚本文件
at -f File.sh 05/18/2024



```





###### lssci

lsscsi 命令来自英文词组 list SCSI 的缩写，其功能是列出 SCSI 设备及属性信息，SCSI 是
一种常用的小型计算机系统接口。lsscsi 命令可以很方便地帮助管理员区分固态硬盘、SATA
硬盘和 FC 硬盘



```shell
# 列出当前系统中全部 SCSI 设备及属性信息
lsscsi

# 查看指定编码的设备属性信息
lsscsi 2:0:0:0

# 查看 SCSI 设备的传输信息
lsscsi -t

#


```





###### pstree

pstree 命令来自英文词组 display a tree of processes 的缩写，其功能是以树状图形式显示进
程信息，可帮助管理员更好地了解进程间的关系。在 Linux 系统中，常用 ps 命令查看进程状
态信息，但是却无法了解进程之间的依赖关系（比如，哪个是父进程，哪个是子进程）。这些
信息可通过 pstree 命令进行查看



```shell
# 以树状图的形式显示当前系统中的全部进程（默认）
pstree

# 以更完整、更丰富的信息样式显示每个进程
pstree -a


```



###### xfs_info

xfs_info 命令来自英文词组 XFS information 的缩写，其功能是查看 XFS 类型设备的详情。
这是一个超简单的命令，在该命令后直接追加设备的名称，即可看到指定 XFS 设备的详情。



```shell
# 查看指定 XFS 设备的详细信息
xfs_info /dev/vda1

# 查看命令工具自身的版本号
xfs_info -V






```



###### nslookup

nslookup 命令来自英文词组 nameserver lookup 的缩写，其功能是查询域名服务器信息。
nslookup 命令能够查询指定域名所对应的 DNS 服务器信息（正向解析），亦可查询指定 DNS
服务器上所绑定的域名信息（反向解析）。该命令有两种工作方式，其一是交互式，在命令行
中执行 nslookup 命令后即可进入，是一问一答的查询模式；其二是非交互式，直接在命令后
追加域名或 IP 地址信息即可进行查询操作



```shell
# 查询指定域名所对应的 DNS 服务器信息（非交互式）
nslookup www.linuxcool.com

# 查询指定域名所对应的 DNS 服务器信息（交互式）
nslookup
www.linuxcool.com

# 在交互查询模式下，设置仅显示域名的邮件交换记录服务器信息
nslookup
>set type=mx
>www.linuxcool.com






```





###### killall

killall 命令的功能是基于服务名关闭一组进程。我们在使用 kill 命令关闭指定 PID 的服务
时，暂且不说要先用 ps 命令找到对应的 PID 才能关闭它，很多服务实际上会发起多个进程，
对应数个不同 PID，用 kill 命令逐一关闭也是一件麻烦事。将 ps 和 kill 两个命令的执行过程
合二为一，就得到了超好用的 killall 命令。管理员只需要给出要关闭的服务名，该命令就能
自动找到该服务所对应的全部进程信息，并关闭它们。



```shell
# 结束指定服务所对应的全部进程
killall httpd

# 打印所有已知信号列表
killall -l



```



###### arping

arping命令来自英文词组ARP ping的缩写，其功能是发送ARP （Address Resolution Protocol，
地址解析协议）请求数据包。arping 命令使用 ARP 数据包来测试网络状态，能够判断某个指定
的 IP 地址是否已在网络上使用，并能够获取更多的设备信息，像是加强版的 ping 命令

```shell
# 测试指定主机的存活状态
arping -f 192.168.10.10

# 向指定主机发送 3 次 ARP 请求数据包
向指定主机发送 3 次 ARP 请求数据包

# 使用指定网口发送指定次数的 ARP 请求数据包后自动退出命令
arping -I ens192 -c 2 192.168.10.10


```



###### w

w 命令来自英文单词 who 的缩写，其功能是显示已登录用户的信息。运维人员只需在命
令终端中输入 w 键并按 Enter 键，即可查看当前系统中已登录的用户列表和他们正在执行的
命令等信息，从而更好地了解系统正在执行的工作，以及等同事都下班后再重启或关闭服务
器，避免突然中断他人工作



```shell
# 显示目前登入系统用户的信息（默认格式）
w

# 显示目前登入系统用户的信息（不显示头信息）
w -h

# 显示当前登录用户的来源
w -f



```



###### host

host 命令的功能是解析域名结果，是一个查找 DNS 解析结果的简单程序。将域名转换成
IP 地址的形式，可帮助运维人员找到指定域名所对应的 IP 地址。



```shell
# 查询指定域名所对应的 IP 地址信息（默认模式）
host www.linuxcool.com

# 查询指定域名所对应的 IP 地址信息（详细模式）
host -v www.linuxcool.com

# 查询指定域名的 MX 邮件类型记录所对应的 IP 地址信息
host -t MX linuxcool.com


```



###### traceroute

traceroute 命令来自英文词组 trace router 的拼写，其功能是追踪网络数据包的传输路径。
执行 tracerouter 命令后会默认发送一个 40 字节大小的数据包到远程目标主机，从远程目标主
机的反馈信息可以得知数据包经过了哪些路径最终到达终点。



```shell
# 追踪本地数据包到指定网站经过的传输路径（默认）
traceroute www.linuxprobe.com

# 追踪本地数据包到指定网站经过的传输路径，跳数最大为 7 次
traceroute -m 7 www.linuxprobe.com

# 追踪本地数据包到指定网站经过的传输路径，显示 IP 地址而不是主机名
traceroute -n www.linuxprobe.com

# 追踪本地数据包到指定网站经过的传输路径，探测包个数为 4 次
traceroute -q 4 www.linuxprobe.com

# 追踪本地数据包到指定网站经过的传输路径，最长等待时间为 3 秒
traceroute -w 3 www.linuxprobe.com






```





###### nice

nice 命令的功能是调整进程的优先级，以合理分配系统资源。工作在 Linux 系统后台的
某些不重要的进程，例如用于定期备份数据、自动清理垃圾等的进程，我们都可以通过 nice
命令调低其执行优先级，把硬件资源留给更重要的进程。进程优先级的范围为−20~19，数字
越小，优先级越高。



```shell
# 以优先级为 5 执行指定脚本
nice -n -5 ./File.sh

# 以最高优先级执行指定脚本
nice -n -20 ./File.sh



```





###### chkconfig

chkconfig 命令来自英文词组 check config 的缩写，其功能是管理服务程序。chkconfig
命令由红帽公司遵循 GPL 开源协议开发而成，能够用于日常管理服务程序的自启动开启、
自启动关闭等工作。随着 RHEL 8/CentOS 8 版本系统的发布，该命令功能逐步被 systemctl
命令替代。



```shell
# 列出当前系统中已有的全部服务名称
chkconfig --list

# 将指定的服务加入开机自启动，重启后默认依然有效
chkconfig telnet on

# 将指定的服务移除出开机自启动，重启后默认不会运行
chkconfig telnet off

# 将指定名称的服务程序加入管理列表
chkconfig --add httpd

# 将指定名称的服务程序移除出管理列表

chkconfig --del httpd


```





###### pgrep

pgrep 命令来自英文词组 process global regular expression print 的缩写，其功能是检索进程
PID。与 pidof 命令必须准确输入服务名称不同，pgrep 命令通过正则表达式进行检索，因此用
户只需要输入服务名称的一部分即可进行搜索操作，在不记得服务程序的全名时特别好用。



```shell
# 检索某名称服务所对应的 PID 信息
pgrep sshd

# 以逗号为间隔符，检索某名称服务所对应的 PID 信息
pgrep -d , sshd

# 指定发起人名称，检索某名称服务所对应的 PID 信息
pgrep -u www sshd
pgrep -u root sshd

```



###### watch

watch 命令的功能是周期性执行任务命令。watch 命令会以周期性的方式执行指定命令，
例如每隔几秒钟、几分钟执行一次，并持续关注命令的运行结果，以免运维人员一遍一遍地
手动运行。



```shell
# 设定每间隔 1s 执行一次指定命令，用于监视系统负载情况
watch -n 1 uptime

# 默认每间隔 2s 执行一次指定命令，用于监视网络链接情况
watch "netstat -ant"

# 默认每间隔 2s 执行一次指定命令，用于监视磁盘使用情况，并高亮显示变化信息
watch -d "df -h"

# 设定每间隔 2min 执行一次指定命令，用于观察文件内容变化情况
 watch -n 120 "cat File.cfg" 
```



###### declare

declare 命令的功能是用于声明定义新的变量。使用 declare 命令创建的变量仅可在当前
Shell 环境下起作用，切换 Shell 环境后将无效。要想在其他 Shell 环境下使用，需要将其提升
为全局环境变量。



```shell
# 显示当前系统中已定义的全部变量信息
declare


# 声明定义一个新的变量
declare URL="www.linuxcool.com"


# 声明定义一个新的变量，其赋值来自运算表达式的结果
declare -i NUM=100+200


# 分别查看两个变量所对应的定义信息
declare -p URL NUM

# 将指定的变量提升为全局环境变量
declare -x URL


```



###### nl

nl 命令来自英文词组 number of lines 的缩写，其功能是显示文件内容及行号。nl 命令具
有类似于“cat -n 文件名”的效果，除此之外，还可以对显示的行号格式进行深度定制。



```shell
# 显示指定文件的内容及行号信息
nl File.cfg


# 显示指定文件的内容及行号信息，空行也加上行号
nl -b a File.cfg

# 空行也算一行，并且行号前面自动补 0，统一输出格式后显示指定文件的内容及行号信息
nl -b a -n rz File.cfg


```







###### iptraf

iptraf 命令来自英文词组 IP traffic monitor 的缩写，其功能是实时监视网卡流量。iptraf 命
令可以用来监视本地网络状况，能够生成网络协议数据包信息、以太网信息、网络节点状态
及 IP 校验和错误等重要信息。



```shell
# 实时监视指定网卡的详细流量状态信息
iptraf -d eth0

# 实时监视指定网卡的 IP 流量信息
iptraf -i eth0

# 实时监视指定网卡上的 TCP/UDP 网络流量信息
iptraf -s eth0




```





###### extundelete

extundelete 命令的功能是恢复文件。extundelete 命令能够恢复分区中被意外删除的文件。
在使用前需要先将要恢复的分区卸载，以防数据被意外覆盖。
经实测，extundelete 命令仅可恢复 EXT3 与 EXT4 格式的文件。



```shell
# 恢复指定分区中的全部文件
extundelete /dev/sdb --restore-all

# 恢复指定分区中的指定文件

extundelete /dev/sdb --restore-file File.img

# 恢复指定分区中的指定目录
extundelete /dev/sdb --restore-directory /Dir






```



###### vnstat

vnstat 命令的功能是查看网卡流量的使用情况，是一个基于控制台的网络流量监控器。
vnstat 命令能够以每小时、每天、每月的时间跨度查看 Linux 系统中网卡流量的使用情况。由
于 vnstat 命令读取的是 proc 目录内系统记录的流量信息，因此即便运维人员没有 root 管理员
身份，也可以用该命令查看系统流量的统计情况。





```shell
# 查询指定网卡的流量使用情况
vnstat -i eth0

# 更新数据库后查看今天的流量使用情况
vnstat -d

# 更新数据库后查看本月的流量使用情况
vnstat -m

# 查看当前实时流量情况
vnstat -l


```





###### pidof

pidof 命令来自英文词组 process identifier of 的缩写，其功能是查找服务进程的 PID。在没
有 pidof 命令之前，Linux 系统运维人员要想获知一个服务进程的 PID，只得先用 ps 命令遍历
整个系统的进程状态，再使用 grep 命令进行查找，不仅操作复杂而且效率也低。现在只需要
在 pidof 命令后加上想查询的服务名称，就会查找到具体信息。



```shell
# 查找某个指定服务所对应的进程 PID
pidof sshd

# 查找多个指定服务所对应的进程 PID
pidof sshd crond

```



###### vmstat

vmstat 命令来自英文词组 virtual memory statistics 的缩写，其功能是监视系统资源状态。
可以使用 vmstat 查看系统中关于进程、内存、硬盘等资源的运行状态，但无法深入分析。vmstat
命令是一款轻量级的性能查看工具，不会给系统带来什么负担。



```shell
# 显示系统整体的资源状态
vmstat -a

# 显示指定的硬盘分区状态
vmstat -p /dev/sda1

# 显示内存分配机制信息（SLAB）
vmstat -m

# 以表格形式显示事件计数器和内存状态
vmstat -s

# 设置每间隔 1s 刷新显示一次系统整体状态信息
vmstat 1


```



###### type

type 命令的功能是查看命令类型。如需区分某个命令是 Shell 内部指令还是外部命令，则
可以使用 type 命令进行查看。

```shell
# 查看某指定别名命令的类型信息
 type ls
 
# 查看某指定 Shell 内部指令的类型信息
type cd

# 查看某指定关键字的类型信息
type if

```





###### iostat

iostat 命令来自英文词组 I/O stat 的缩写，其功能是监视系统 I/O 设备的使用情况。iostat
命令能够查看硬盘活动的统计情况，也能显示 CPU 的使用情况，可帮助 Linux 系统运维人员
进行系统调优。



```shell
# 每隔 2s 报告一次系统硬盘的使用情况
iostat -d 2

# 每隔 2s 报告一次指定硬盘的使用情况，总共报告 6 次
iostat -x vda -d 2 6




```





###### zenity

zenity 命令的功能是显示图形框，可允许运维人员调用各种 Shell 终端的弹窗信息，以查
看日历、消息，亦可以让用户输入信息或密码进行保存。Zenity 命令的玩法很多，可根据需
求进行深度开发。



```shell
# 显示日历框
zenity --calendar

# 弹框显示进度栏
zenity --progress

# 显示密码框
zenity --password

```



###### jobs

jobs 命令的功能是显示终端后台的作业信息。可以使用 jobs 命令查看当前系统中终端后
台的任务列表及其运行状态，查看任务列表及对应的进程 ID，简单方便地了解当前有哪些工
作正在后台运行。



```shell
# 显示当前后台的作业列表
jobs

# 显示当前后台的作业列表及进程 ID
jobs -l

# 仅显示运行的后台作业
jobs -r

# 仅显示已暂停的后台作业
jobs -s

# 仅显示上次执行 jobs 命令后状态发生变化的后台作业
jobs -n

```



###### lscpu

lscpu 命令来自英文词组 list the CPU architecture 的缩写，其功能是显示 CPU 架构信息。
lscpu 命令会从/proc/cpuinfo 文件中收集有关本机 CPU 架构的信息，并整理成易读的格式输
出到 Shell 终端，以方便运维人员了解本机 CPU 数量、架构、线程、核心、套接字等重要指
标信息。



```shel
# 显示有关 CPU 架构的信息
lscpu

# 




```



###### swapon

swapon 命令的功能是激活交换（swap）分区。交换分区是一种在服务器物理内存不够的
情况下，将内存中暂时不用的数据临时存放到硬盘空间的技术，目的是让物理内存一直保持
高效，总是在处理重要数据（与 Windows 系统中 pagefile.sys 虚拟内存文件的作用一样）。
swapon 命令用于激活 Linux 系统中已存在的交换分区，让交换分区内存可以被立即使用，
但要想永久生效，还是需要将挂载信息写入/etc/fstab 文件。



```shel
# 查看已有的指定交换分区的信息
swapon -v /dev/mapper/rhel-swap

# 查看当前已有交换分区的使用情况
swapon -s

# 对指定的交换分区设置优先顺序
swapon -p 3 /dev/dm-1

# 立即激活所有/etc/fstab 文件中定义过的交换分区
swapon -a





```



###### paste

paste 命令的功能是合并两个文件。paste 命令能够将两个文件以列对列的方式进行合并
（相当于是把两个不同文件的内容粘贴到了一起），形成新的文件。如需先将内容合并成一行，
再以行粘贴的方式合并，可以使用-s 参数搞定。



```shell
# 现有两个文件（File1 和 File2），对其进行合并操作
cat paste File1 File2

# 设置合并后内容的分隔符，再进行合并操作
 paste -d: File1 File2
 
# 设置每个文件内容为一行，再进行合并操作
paste -s File1 File2


```





###### restorecon

restorecon 命令来自英文词组 restore config 的缩写，其功能是恢复文件安全上下文。安全上
下文是 SELinux 安全子系统中重要的安全控制策略。在 Linux 系统中一切都是文件，而 SELinux
安全子系统中则一切都是对象，所有的文件、系统端口和进程都具备安全上下文策略。
一般情况下，使用 cp 命令对文件进行复制操作后，新的文件不会保留原始属性（除非加
了-p 参数），此时需要使用 restorecon 命令恢复新文件的安全上下文。此外，使用 semanage
命令对文件的安全上下文策略进行修改后，如果想让新的安全上下文生效，也需要用到
restorecon 命令。



```shell
# 恢复指定文件的安全上下文，并显示过程信息
restorecon -v /Dir/File.txt

# 恢复指定目录的安全上下文
restorecon -R /Dir






```



###### semanage

semanage 命令来自英文词组 SELinux manage 的缩写，其功能是查询与修改安全上下文。
semanage 的功能类似于 chcon 命令，它们都可以用于设置文件的 SELinux 安全上下文策略，
但 semanage 命令的功能更强大一些，还能够对系统端口、进程等 SELinux 域策略进行查询和
修改，因此更推荐使用。
设置过安全上下文后需要使用 restorecon 命令让新设置立即生效。



```shell
# 对指定目录和文件添加新的 SELinux 安全上下文
semanage fcontext -a -t httpd_sys_content_t /Dir/wwwroot
semanage fcontext -a -t httpd_sys_content_t /Dir/wwwroot/*

# 查询指定服务所对应的 SELinux 域允许端口列表
semanage port -l | grep http

# 对指定服务所对应的 SELinux 域允许端口列表添加新的值
semanage port -a -t http_port_t -p tcp 6111





```







###### poweroff

poweroff 命令的功能是关闭操作系统。很多读者会着迷于对比 poweroff、halt、shutdown、
init 0 等命令之间的区别，它们其实都是 Linux 系统中的关机命令，体验上没有区别，更多地
是依据个人喜好来选择的。



```shell
# 关闭操作系统
poweroff

# 模拟关机操作并记录过程到日志文件（没有真正关机）
poweroff -w

# 将所有的硬件设置为备用模式，并关闭操作系统
poweroff -h




```



###### blkid

blkid 命令来自英文词组 block ID 的缩写，其功能是显示块设备信息。blkid 命令能够查看
Linux 系统中全部的块设备（也就是我们俗称的硬盘或光盘设备）信息，并可以依据块设备名
称、文件系统类型、硬盘卷标、UUID 等进行信息检索



```shell
# 显示当前系统中全部的块设备信息（名称、UUID、文件系统类型等）
blkid

# 显示指定块设备所对应的 UUID 信息
blkid -s UUID /dev/sda1

# 以列表方式显示当前系统中全部块设备信息
blkid -o list

# 显示系统中所有块设备的名称信息
blkid -o device

# 显示系统中所有块设备的文件系统类型信息
blkid -s TYPE

# 显示系统中所有块设备的 LABEL 信息
blkid -s LABEL

```





###### dmesg

dmesg 命令来自英文词组 display message 的缩写，其功能是显示开机过程信息。Linux 系
统内核会将开机过程信息存储在环形缓冲区（ring buffer）中，随后再写入/var/log/dmesg 文件。
如果开机时来不及查看这些信息，则可以利用 dmesg 命令进行调取。



```shell
# 显示全部的系统开机过程信息
dmesg

# 显示与指定硬盘设备相关的开机过程信息
dmesg | grep sda

# 显示与内存相关的开机过程信息
dmesg | grep memory

# 清空环形缓冲区中已有的日志内容
dmesg -c

```





###### hwclock



hwclock 命令来自英文词组 hardware clock 的缩写，其功能是显示与设置系统硬件时钟。
hwclock 是一个硬件时钟管理工具，可以用于显示当前时间、设置系统硬件时钟与系统时钟的
同步。
系统硬件时钟是指电脑主板上的时钟信息，通常会被写入 BIOS，而系统时钟则是指内核
中的时钟信息。Linux 系统在启动时会由内核读取系统硬件时钟的信息，随后系统时钟便独立
运作，Linux 相关函数及指令都会依据该时间工作。



```shell
# 显示当前系统硬件时钟
hwclock

# 同步系统硬件时钟与系统时钟
hwclock --systohc

# 显示系统硬件时钟及版本信息
hwclock --version

# 


```



###### shift

shift 命令的功能是向左移动参数。Linux 命令能够一次性接收多个参数，可能是 0 个、5
个，也可能是 15 个，那么该如何逐一处理这些参数呢？
shift 能够将命令接收到的参数逐个向左移动一位，即原本的$3 变量会覆盖$2 变量，原本
的$2 变量会覆盖$1 变量，这样我们只需每执行一次 shift 命令后调用一次$1 变量，就能够实
现对全部参数的处理工作了



```shell
# 逐一输出在执行 shift 命令后的$1 变量的值，直至清空全部参数
#!/bin/bash
while [ $# != 0 ] ; do
	echo "$1"
	shift
done


# 执行脚本
./File.sh AA BB CC DD

```





###### sysctl

sysctl 命令来自英文词组 system control 的缩写，其功能是配置系统内核参数。sysctl 命
令能够在 Linux 系统运行时动态地配置系统内核参数，包含 TCP/IP 堆栈和虚拟内存系统等
选项，可让有经验的系统管理员更好地优化整台服务器性能，但请注意，配置结果仅在当
前生效，系统重启后参数将恢复到初始状态，要想永久生效，则需要将参数写入
/etc/sysctl.conf 系统文件。



```shell
# 查看系统中所有内核参数变量和值
sysctl -a


# 读取一个指定系统内核参数变量的值
sysctl dev.cdrom.debug

# 修改一个指定系统内核参数变量的值
sysctl dev.cdrom.debug=1

# 


```







###### dig

dig 命令来自英文词组 domain information groper 的缩写，其功能是查询域名 DNS 信息。
dig 命令能够便捷地查询指定域名所对应的 DNS 服务器信息，具有灵活性好易用、输出清晰
等特点，与 nslookup 命令很相似。



```shell
# 查询指定域名所对应的 DNS 信息
dig www.linuxcool.com

# 查询指定 IP 地址所对应的域名信息（反向查询）
dig -x 39.98.160.17

# 指定要查询的数据类型（邮件），查询指定域名所对应的 DNS 信息
dig -t MX linuxcool.com



```



###### sar

sar 命令来自英文词组 system activity reporter 的缩写，其功能是统计系统的运行状态。可
以使用 sar 命令对 Linux 系统进行取样，且大量的取样数据和分析结果会实时存入文件中，因
此不会消耗太多的内存和额外的系统资源。



```shell
# 统计 CPU 设备的负载信息，每次间隔 2s，共 3 次
sar -u 2 3

# 统计硬盘设备的读写信息，每次间隔 2s，共 3 次
sar -d 2 3

# 统计内存设备的读写信息，每次间隔 2s，共 3 次
sar -r 2 3

# 统计内存设备的分页使用情况，每次间隔 5s，共 3 次
sar -B 5 3

# 显示 CPU 利用率情况
sar -u

# 显示系统负载情况

sar -q

# 显示硬盘 I/O 和传输速率情况
sar -b

# 显示网卡和网络情况
sar -n DEV







```





































# 高级篇

## 内存管理



## 进程调度



## 进程通信



## 网络管理

### 网络模型



### 防火墙



## 文件系统

虚拟文件系统（VFS）



## 磁盘管理

### LVM

![image-20240601142438637]（./assets/image-20240601142438637.png）

![image-20240601142405519]（./assets/image-20240601142405519.png）

![image-20240601142001215]（./assets/image-20240601142001215.png）

###### lvm是什么

​	LVM是逻辑盘卷管理（Logical Volume Manager）的简称，它是Linux环境下对磁盘分区进行管理的一种机制，LVM是建立在硬盘和分区之上的一个逻辑层，来提高磁盘分区管理的灵活性。

###### lvm优点

* 可以灵活分配和管理磁盘空间
* 可以对分区进行动态的扩容
* 可以增加新的磁盘到lvm中

### RAID



###### 常用的RAID级别比较

![image-20240601143216333]（./assets/image-20240601143216333.png）







