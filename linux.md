## MBR和GPT

*GPT和MBR是不同的分区表类型*；使用MBR分区表的硬盘最多只能划分4个主分区磁盘，如果分区的硬盘容量超过2TB了，则需要使用GPT分区表类型，是一种新的硬盘分区标准；MBR的意思是主引导记录，它有自己的启动器，也就是启动代码，GPT是一个正逐渐取代MBR的新标准，它由UEFI辅住而形成的

## sda和vda

主机中实际硬盘位stat接口的，表示为sd[a-d]（在MBR模式下）。

假设你的主机为虚拟机，里面仅有一颗VirtIO接口的磁盘，他在Linux操作系统里面的设备文件名为vda

## 挂载点

比如说根目录/，可以说选择挂载点/，容量为20GB。

```
由于 XFS filesystem 最顶层的目录之 inode 一般为 128 号，因此可以发现 /,/boot, /home 为三个不同的 filesystem 啰！ （因为每一行的文件属性并不相同，且三个目录的挂载点也均不相同之故。）p358
```

为什么这样说？？？而且我自己试验时，/home的.和..的inode确实一样，但是使用cd ..仍然会到/目录去。

df 之后出现的filesystem，比如`/dev/mapper/centos-root `，或者`tmpfs`表示什么意思？具体设备？具体分区？还是一种文件类型（但是又不会存在xfs）？

## 挂载

将一个设备和一个挂载点联系起来。比如将/dev/sda挂载到/。

把目录想象成快捷方式，分区是实际存储数据的地方，如果将/dev/sda挂载到/test1和/test2，那么打开两个目录，会看见一样的东西，即/dev/sda分区里的内容，数据也只有原来分区里的一份。

## 分区配置

所有刚刚装系统配置的东西，包括 root 的密码等等，通通都会被纪录到 /root/anaconda-ks.cfg 这个文件内喔。

google找 kickstart 这个关键字，会得到很多协助喔

## 文件与目录权限

* 目录的权限：

>  r 是可以读取目录中的文件列表，就像抽屉里有灯一样。
>
> w 是可以编辑目录，可以删除目录中的文件。就像这个抽屉是我的，就算我抽屉里的东西不是我的，也可以扔掉。
>
> x 是可以进入目录进行工作。必须要有目录的x权限，才可以对目录中的文件进行读取、修改、执行等操作。注意就算没有r权限，只有x权限，只要知道文件名字也是可以进行操作的。

* 文件权限：

> chown、chgrp、chmod

## ls

ls -d test* 才可以显示本目录下test开头的目录，那我怎么知道要加 test* 这个参数？man手册也没写要加参数。

ls -l 长信息、ls -a 所有信息、ls -d 只列出目录

## pwd、rmdir、mkdir

pwd：显示所在工作路径。

>  pwd -P 可以查询真实路径，即如果本路径是链接文件，可以找到链接的真实地址

mkdir：创建空目录。

> mkdir -m可以设置权限：mkdir -m 777 test 

>  mkdir -p 可以创建在没有目录的地方创建新目录：mkdir -p test1/test2/test3

rmdir：删除空目录。

> 接着刚刚创建的目录，继续输入rmdir -p test1/test2/test3 可以直接删掉test1

## cp、rm、mv

cp

## 分区、文件系统、挂载点

分区是针对磁盘进行的。在完成了分区后的分区（在linux中类似于/dev/sda，/dev/sdb1，/dev/sdb2等的设备就是完成了分区后的分区）进行文件系统初始化（也就是常说的格式化），再把文件系统挂载到制定目录。

1. 查看指定 **“文件系统”** 的整体信息（主要是对superblock进行查询）：

* blkid

```shell
/dev/sda2: UUID="65fd77a4-9ba1-4f38-9f3a-2950c03be2ee" TYPE="xfs" PARTUUID="4c604443-e940-4908-98eb-12db2e7e90b6" 

/dev/sda3: UUID="SNfPIA-f2WC-FzWc-bwNx-cCPe-dzkI-ATrhnF" TYPE="LVM2_member" PARTUUID="b768fcbe-aa0f-4546-aa74-7a60cde38323" 

/dev/mapper/centos-root: UUID="1961737b-c19b-422a-a0aa-9e2d3b5d3a28" TYPE="xfs" 

/dev/mapper/centos-swap: UUID="5c6a4fdf-a774-4a2f-8e3c-83a56a2569e5" TYPE="swap" 

/dev/mapper/centos-home: UUID="1e1228ba-eeaf-4e63-87aa-a74c3e305e05" TYPE="xfs"
```

这里的 /dev/sda2，/dev/mapper/centos-home 都是分区（即设备名）

* dumpe2fs 分区名

查询 Ext 家族 superblock 信息的指令，注意，只能查询使用ext文件系统的格式化的分区。

* xfs_info 分区名 或 挂载点

查询该分区或该挂载点下文件系统的信息，只能查询使用xfs文件系统的格式化的分区信息



2. 查询“磁盘”与目录容量

* **df**  （即disk free。作用是 列出已经挂载好的文件系统的磁盘使用情况）

```shell
[root@study ~]# df [-ahikHTm] [目录或文件名]
选项与参数：
-a ：列出所有的文件系统，包括系统特有的 /proc 等文件系统；
-k ：以 KBytes 的容量显示各文件系统；
-m ：以 MBytes 的容量显示各文件系统；
-h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；
-H ：以 M=1000K 取代 M=1024K 的进位方式；
-T ：连同该 partition 的 filesystem 名称 （例如 xfs） 也列出；
-i ：不用磁盘容量，而以 inode 的数量来显示

```

结果如下：

```shell
[jrk@localhost test]$ df
文件系统                   1K-块    已用    可用 已用% 挂载点
devtmpfs                 1913564       0 1913564    0% /dev
```

* **du** （即disk usage，作用是查询指定文件夹的大小）

```shell
[root@study ~]# du [-ahskm] 文件或目录名称
选项与参数：
-a ：列出所有的文件与目录容量，因为默认仅统计目录下面的文件量而已。
-h ：以人们较易读的容量格式 （G/M） 显示；
-s ：列出总量而已，而不列出每个各别的目录占用容量；
-S ：不包括子目录下的总计，与 -s 有点差别。
-k ：以 KBytes 列出容量显示；
-m ：以 MBytes 列出容量显示；

```

结果如下：

```shell
[jrk@localhost test]$ du -a
44	./test12
44	.
```

3. 链接

* 符号链接（symbolic link）：

例如：`ln -s /etc/crontab crontab2` 

意思是`对/etc/crontab 创建快捷方式，名称叫crontab2`

* 硬链接（hard link）：

例如：`ln /etc/crontab .`

意思是`在本文件夹下创建一个和原文件同名的硬链接`

|                           符号链接                           |      |                            硬链接                            |
| :----------------------------------------------------------: | ---: | :----------------------------------------------------------: |
| 类似于windows的创建快捷方式，会实际产生一个文件，文件的block中存的是链接的地址。 |      |           直接将该文件的inode指向目标文件的inode。           |
|                  会占用磁盘，是一个独立文件                  |      |              不占用磁盘block，不是一个独立文件               |
|                        可以链接文件夹                        |      |                       不可以链接文件夹                       |
|            删除快捷方式就是单纯删除了快捷方式文件            |      | 删除该文件会使原文件inode的数量减一，原文件只有inode为0时才被删除 |
|                                                              |      |                                                              |

分区相关命令：
