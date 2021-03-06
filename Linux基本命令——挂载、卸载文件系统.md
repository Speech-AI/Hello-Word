# Linux基本命令——挂载、卸载文件系统

转自

<https://baijiahao.baidu.com/s?id=1615839265668669120&wfr=spider&for=pc>



前言：在Linux系统中，对各种存储设备中的资源访问（如读取、保存文件等）都是通过目录结构进行的，虽然系统核心能够通过“设备文件”的方式操纵各种设备，但是对于用户来说，还需要增加一个“挂载”的过程，才能像正常访问目录一样访问存储设备中的资源。

**1.mount命令**：挂载文件系统、ISO镜像到指定文件夹

**命令格式**：

mount [ -t 类型 ] 存储设备 挂载点目录

mount -o loop ISO镜像文件 挂载点目录

**2.umount命令**：卸载已挂载的文件系统

**命令格式：**

umount 存储设备位置

umount 挂载点目录

**示例：**

**（1）光盘设备挂载、卸载示例：**



![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=483871122,2998760256&fm=173&app=49&f=JPEG?w=640&h=423&s=80735F905D9C7DED5E6534DB030010F0)

**（2）****ISO镜像文件挂载示例：**

mount -o loop rhel-server-6.5-x86_64-dvd.iso /media/mnt

注：“iso”镜像文件通常被视为一种特殊的“回环”文件系统，因此挂载时需要添加“-o loop”选项。

**（3）****卸载文件系统示例：**



![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=49501044,3004115366&fm=173&app=49&f=JPEG?w=640&h=143&s=0221DB00D292A592E86F545B0300C091)

注：由于同一个设备可以被挂载到多个目录下，所以一般建议通过挂载点的目录位置来进行卸载。

**3.设置文件系统的自动挂载：**

系统中的/etc/fstab文件可以视为mount命令的配置文件，其中存储了文件系统的静态挂载数据。系统在每次开机时，会自动读取这个文件的内容，自动挂载所指定的文件系统。

![img](https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=4162684287,3603012353&fm=173&app=49&f=JPEG?w=640&h=388&s=3E0675231B1C74C85CDD01DA0000D0B3)

在/etc/fstab文件中，每一行记录对应一个分区或设备的挂载配置信息，从左到右包括六个字段（使用空格或制表符分隔）。

第1字段：设备名或设备卷标名

第2字段：文件系统的挂载点目录的位置

第3字段：文件系统类型，如EXT4、Swap等

第4字段：挂载参数，即mount命令“-o”选项后可使用的参数。例如，defaults、rw、ro、noexec分别表示默认参数、可写、只读、禁用执行程序

第5字段：表示文件系统是否需要dump备份（dump是一个备份工具）。一般设为1时表示需要，设为0时将被dump所忽略

第6字段：该数字用于决定在系统启动时进行磁盘检查的顺序。0表示不进行检查，1表示优先检查，2表示其次检查。对于根分区设为1，其他分区设为2

**通过在“/etc/fstab”文件中添加相应的挂载配置，可以实现开机后自动挂载指定的分区。**

**示例：**



![img](https://ss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=1587672227,412668986&fm=173&app=49&f=JPEG?w=640&h=158&s=81335F90CA4CFD0350FC81D10300D0F3)

**注意命令格式中的空格，有些地方有空格，但上传之后空格就不明显了。**

## linux下查看磁盘分区的文件系统格式
**df -T 只可以查看已经挂载的分区和文件系统类型**

Filesystem Type 1K-blocks Used Available Use% Mounted on
/dev/sda1 ext4 20642428 3698868 15894984 19% /
tmpfs tmpfs 32947160 0 32947160 0% /dev/shm


**fdisk -l 可以显示出所有挂载和未挂载的分区，但不显示文件系统类型**

Disk /dev/sda: 299.4 GB, 299439751168 bytes
255 heads, 63 sectors/track, 36404 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000576df

Device Boot Start End Blocks Id System
/dev/sda1 * 1 2611 20971520 83 Linux
/dev/sda2 2611 3134 4194304 82 Linux swap / Solaris
/dev/sda3 3134 36404 267248282 83 Linux


**parted -l 可以查看未挂载的文件系统类型，以及哪些分区尚未格式化**

Model: LSI MR9240-8i (scsi)
Disk /dev/sda: 299GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number Start End Size Type File system Flags
1 1049kB 21.5GB 21.5GB primary ext4 boot
2 21.5GB 25.8GB 4295MB primary linux-swap(v1)
3 25.8GB 299GB 274GB primary ext4


**lsblk -f 也可以查看未挂载的文件系统类型**

NAME FSTYPE LABEL UUID MOUNTPOINT
sda 
|-sda1 ext4 c4f338b7-13b4-48d2-9a09-8c12194a3e95 /
|-sda2 swap 21ead8d0-411f-4c23-bdca-642643aa234b [SWAP]
`-sda3 ext4 2872b14e-45va-461e-8667-43a6f04b7bc9

 

file -s /dev/sda3

/dev/sda3: Linux rev 1.0 ext4 filesystem data (needs journal recovery) (extents) (large files) (huge files)
