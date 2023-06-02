## shell基本命令

### 查看目录和文件

```bash
pwd  #显示当前所在位置，绝对路径

```

改变目录：cd

```bash
cd 路径 #进入路径下目录

cd .. #进入当前目录的上一级目录，.表示当前目录，～表示户主目录
```

列出目录内容：ls

```bash
ls [option] [file]
# 用-a显示所有文件，-l查看文件的各种属性
```

列出目录内容：dir和vdir
dir 除了功能比ls少，其他都一样
vdir：相当于 ls -l

查看文本文件：cat和more

```bash
cat 文件名

more 文件名 #文本长的文件用more，它可以显示百分比

```

阅读文件开头和结尾：head和tail

```bash
head -n num 文件名
tail -n num 文件名

```

更好的文本阅读工具：less

查找文件内容：grep

```bash
grep [options] pattern [file] #pattern：搜索关键字
```

### find命令

```bash
find [option] [path] [expression]
find /usr/ -name zip -print
#path为查找范围，-name指定文件名，-print将结果输出到stdout
#搜索文件没有权限，会出现：Permission denied
# -type指定文件类型，-atime n查找最后一次使用在n天前的文件
# -mtime n查找最后一次修改在n天前的文件，+n大于n天，-n小于n天
```

快速定位：locate

    locate *.doc

### 从终端运行程序

```bash
firefox #启动火狐浏览器

firefox & #在命令后面加上&，程序会在后台继续运行，终端继续接受用户输入
```

### 查找特定程序：whereis

```bash
whereis xxx 
#如果无法找到返回空字符串

```

### 用户及版本信息查看

```bash
who #当前系统有哪些人登陆，在那个工作台
whoami
uname #当前系统的版本信息，-a显示全部，-r只显示内核版本信息

```

寻找帮助：man

```bash
man #获得命令的帮助信息
```

获取命令简介whatis和apropos

```bash
whatis xxx #获取命令的简单介绍

apropos xxx #根据简单介绍找命令

```

## 文件目录管理

### 建立目录：mkdir

```bash
mkdir 目录名
# -p 建立路径下的所有目录
touch 文件名 #新建空文件，可以用于更新旧文件的建立时间
```

移动文件：mv

```bash
mv 文件名 地址 #有同名文件会进行覆盖，-i加提示
```

### 复制文件和目录：cp

```bash
cp 文件名 地址 #将文件复制到该地址下

rmdir 文件名 #只能删除空文件
rm 文件名    #可以删除多个文件
-i           #删除前给提示
-r           #删除目录下的所有文件
```

### `文件属性和权限`

```bash
ls -l /bin
-rwxr-xr-x 1 root root 38096 2008-11-13 14:45 /bin
```

第一位-表示文件为普通文件，rwxr-xr-x是三组权限位，为：rwx，r-x，r-x，分别表示属主，属组，其他人的权限，没有的权限用-代替，后面的数字1表示文件连接数目。

第3，4字段表示属主，属组。

后面分别表示文件大小，最后修改日期和时间，文件完整路径

### Linux文件类型

| 文件类型   | 符号 | 文件类型   | 符号 |
| :----- | :- | :----- | :- |
| 普通文件   | -  | 本地域套接口 | s  |
| 目录     | d  | 有名管道   | p  |
| 字符设备文件 | c  | 符号链接   | l  |
| 块设备文件  | b  |        |    |

### 改变文件所有权：chown和chgrp

```bash
chown -r 文件名 [owner][:group] #-r改变一个目录下的所有文件权限
chgrp 文件名 [group]            #专门设置文件的属组
```

&#x20;owner为更改文件的所有组，group为所有组，两者可以选择省略

```bash
chmod u+x days
chmod a-x days
chmod ug=rw,o=r days 
```

文件属主（u），属组（g），其他人（o）和所以人（a）

### 建立连接：ln

```bash
ln -s target link_name   #对target文件建立软连接，通过link_name可以访问target
```

### 输入输出重定向和管道

程序在默认情况下输出结果的地方被称为标准输出（stdout）

```bash
ls > ~/ls-out   #输出重定向，将在stdout输出的内容，输出到文件中
#若文件不存在则创建文件，若存在则将文件内容清空
# >> 会保留原来文件的内容
```

程序在默认情况下接受输入的地方被称为标准输入（stdin）

```bash
cat <days  #从文件中获得输入
#使用 << 立即文档告诉Shell从键盘接受输入，并传递给程序
```

管道：通过一根竖线“|”，将命令的输出连接到另一条命令输入

```bash
ls | grep ay     #将ls的输出用于grep的输入
```

### &#x20;管理.deb软件包：dpkg

这个软件包主要用于Debian和Ubuntu

```bash
dpkg -install #下载

dpkg -l 软件       #查看版本信息

dpkg -s       #查看软件的文件

sudo dpkg --remove   #删除软件
```

### &#x20;管理RPM软件包\:rpm

用于管理.rpm格式的软件包

```bash
sudo rpm -i -v -h xxx.rpm   #下载
sudo rpm -Uvh xxx.rpm       #更新
rpm -q xxx                  #查看已安装的软件包
sudo rpm -e xxx -vv --test  #卸载

```

\-v显示rpm当前正在执行的工作，-h告知用户安装进度,可以省略-。

\-vv输出完整的调试信息，--test模拟删除过程但不真的删除。

### 高级软件包工具：APT

它可以自动检测软件依赖问题，下载和安装所有文件

APT工具最常用的有两个命令：apt-get和apt-cache。前者用于执行，后者用于查找。

系统第一次启动时，需要运行apt-get update更新当前apt-get缓存中的软件包信息。

```bash
apt-get install       #下载并安装
apt-get upgrade       #下载并安装系统上已有软件包的最新版本
apt-get remove        #卸载特定的软件包
apt-get source        #下载特定的软件源代码
apt-get clean         #删除所有已下载的包文件
apt-get -h            #查看apt-get的全部用法
```

查看软件包信息

```bash
apt-cache search flight     #搜索带有“flight”的安装包
apt-cache depends flighter  #查看flighter的依赖关系
```

apt-get的安装源都放在/etc/apt/sources.list中

### 从源代码编译软件

```bash
tar zxvf xxx.tar        #解压安装包
./configure --prefix=xx #配置软件，--prefix指定软件安装的位置
make                    #编译源代码
sudo make install       #安装软件
```

## 磁盘管理

### 挂载文件系统：mount命令

```bash
sudo mount 分区 目标目录
```

也可以用-t指明设备需要所需要使用得文件系统类型

\-r和-w分别指定只读模式和可读写模式

### 查看磁盘使用情况：df

df命令会收集和整理当前已经挂载的全部文件系统的一些重要的统计数据

```bash
df
df -t xx          #显示特定的文件系统
```

### 检查和修复文件系统：fsck

```bash
sudo fsck xxx
sudo fsck -p      #fsck命令会读取fstab文件来确定检查哪些文件系统
```

### 在磁盘上建立文件系统：mkfs

```bash
sudo mkfs -t ext4  /dev/sdb1   #将分区格式化为ext4格式
sudo mkfs -t ext4 -c xxx       #-c检查指定设备上损坏的块
```

如果硬盘分区已经挂载到文件系统中，那么在格式化之前必须用umount命令卸载该分区

### 使用USB设备

```bash
sudo mount xxx  xxx
sudo umount xxx           #卸载设备
lsusb                     #列出已发现的usb设备
```

### 压缩工具

压缩文件：gzip

```bash
gzip xxx.tar       #gzip会给被压缩的文件后加上一个“gz”扩展名
gunzip xxx.tar.gz
gzip -d xxx.tar.gz  #解压文件
```

### 存档工具

文件打包：tar

```bash
tar -cvf shell.tar shell/   #把文件打包成shell.tar

tar -xvf
```

\-c指导tar创建归档文件，-v显示执行过程，-f指定归档文件的文件名,-x解开归档文件，-w归档前询问意见

\-z自动调用gzip程序完成相关操作

tar命令选项前的-可以省略。

## 进程管理

### 进程的属性

PID：进程的ID号

PPID：父进程的PID

UID和EUID：真实和有效的用户ID

GID和EGID：真实和有效的组ID

优先级：决定其受到CPU“优待”的程度

谦让度：与优先级相反的概念，用户可以设计它来影响内核的看法

### 监视进程：ps命令

```bash
ps aux          #显示当前系统上运行的所有进程的信息
ps lax          #多提供父进程和谦让度
```

### 即时跟踪进程信息：top命令

### 查看占用文件的进程：lsof

```bash
lsof 文件名
```

### 向进程发送信号：kill

```bash
kill [-signal] pid    #标准语法
kill -l            #显示所有信号及其编号
```

常用的信号：

| 信号编号 | 信号名  | 描述         |
| :--- | :--- | :--------- |
| 0    | EXIT | 程序退出时收到该信号 |
| 1    | HUP  | 挂起         |
| 2    | INT  | 中断         |
| 3    | QUIT | 退出         |
| 9    | KILL | 杀死         |
| 11   | SEGV | 段错误        |
| 15   | TERM | 软件终止       |

### 调整进程的谦让度：nice和renice

```bash
nice -n 2 bc      #设置bc以谦让度增量2启动，不带值默认值为10
renice +12 -p 8596    #renice在进程运行时调整谦让度值，-p指定进程的PID
```

### /PROC文件系统

该文件存放着内核有关系统状态的各种有意义的信息
