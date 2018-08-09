---

title: LVM拓容
date: 2018-8-8 08:12:00
tags: [Linux,笔记,LVM]
categories: [笔记]

---

---

# 拓容之前 

虚拟机上为Centos新建一个20G的磁盘，并重启

查看磁盘以及查看磁盘分区挂载情况
```vim
fdisk -l
df -Th
```
在没有用fdisk命令新建磁盘之前没有详细磁盘信息
```vim
[root@dc1 ~]# fdisk -l
...
Disk /dev/sdb: 21.5 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xc1d53501
...
[root@dc1 ~]# df -Th
Filesystem           Type   Size  Used Avail Use% Mounted on
/dev/sda5            ext4    18G  2.1G   15G  13% /
tmpfs                tmpfs  491M     0  491M   0% /dev/shm
/dev/sda1            ext4   194M   29M  155M  16% /boot
/dev/sda2            ext4   2.0G   35M  1.8G   2% /home
```

---

---

# 创建LVM的磁盘分区
```vim
printf "n\np\n1\n\n\nt\n8e\nw\n" | fdisk /dev/vdb
```
```vim
#fdisk操作命令大全
command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)
```
8e 表示Linux LVM 这一步不可以省
输入fdisk /dev/vdb显示以下信息:
```vim
...
   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1               1        2610    20964793+  8e  Linux LVM
...
```

---

---

# LVM操作
为两个分区创建物理卷
```vim
pvcreate /dev/sdb1
pvcreate /dev/sdc1
```
创建逻辑卷组
```vim
vgcreate lvhd /dev/sdb1
```
激活卷组
```vim
vgchange -ay lvhd
```
创建逻辑卷，使用全部sdb1的容量
```vim
lvcreate -l 100%FREE -n app lvhd
```
或者通过查询PE来创建，-l后的数字可以通过lvdisplay查询的逻辑卷组信息确定
```vim
lvcreate -l 7678  lvhd  -n  app  
```
将1vhd/app分区格式化为ext4格式 -t表示文件类型
```vim
mkfs -t ext4 /dev/lvhd/app
```
挂载分区
```vim
mkdir /app
mount /dev/lvhd/app /app
echo "/dev/lvhd/app    /app    ext4  defaults        0 0" >>/etc/fstab 
```
mount命令用于加载文件系统/dev/lvhd/app到指定的加载点/app。
当Linux系统下划分了新的分区后，需要将这些分区设置为开机自动挂载，否则，Linux是无法使用新建的分区的。 /etc/fstab 文件负责配置Linux开机时自动挂载的分区
```vim
 <file system> <mount point> <type> <options> <dump> <pass>
"/dev/lvhd/app    /app    ext4  defaults        0 0"  //参数对应
```

---

---

# 新添加的一块盘如何扩展到卷组上
```vim
fdisk /dev/sdc      //步骤和之前一样，得到/dev/sdc1
vgextend lvhd /dev/sdc1		//拓展容量
lvcreate -l +100%FREE -n app lvhd 	//逻辑卷扩容,注意此时需要在100%FREE前面带上+号，若用Total PE方式则不用
resize2fs /dev/lvhd/app 	//操作之后既可以用df -Th查看到磁盘信息
```
```vim
[root@dc1 ~]# df -Th
Filesystem           Type   Size  Used Avail Use% Mounted on
/dev/sda5            ext4    18G  2.1G   15G  13% /
tmpfs                tmpfs  491M     0  491M   0% /dev/shm
/dev/sda1            ext4   194M   29M  155M  16% /boot
/dev/sda2            ext4   2.0G   35M  1.8G   2% /home
/dev/mapper/lvhd-app ext4    40G  180M   56G   1% /app
```

---

参考网站:[地址](https://blog.csdn.net/kjsayn/article/details/52996313)