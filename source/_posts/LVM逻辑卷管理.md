title: article title
date: 2018-8-7 08:12:00
tags: [Linux,笔记,磁盘管理]
categories: [Linux]

# LVM逻辑卷管理

---

##LVM工作原理

LVM在每个物理卷头部都维护了一个metadata，每个metadata中都包含了整个VG（volume group：卷组）的信息，包括每个VG的布局配置，PV（physical volume：物理卷）的编号，LV（logical volume：逻辑卷）的编号，以及每个PE（physical extends：物理扩展单元）到LE（logical extends：逻辑扩展单元）的映射关系。同一个VG中的每个PV头部的信息都是相同的，这样有利于故障时进行数据恢复。
LVM对上层文件系统提供LV层，隐藏了操作细节。对文件系统而言，对LV的操作与原先对partition的操作没有差别。当对LV进行写入操作的时候，LVM定位相应的LE，通过PV头部的映射表将数据写入到相应的PE上。LVM实现的关LVM最大的特点就是可以对磁盘进行动态管理。因为逻辑卷的大小是可以动态调整的，而且不会丢失现有的数据。我们如果新增加了硬盘，其也不会改变现有上层的逻辑卷。键在于PE和LE之间建立映射关系，不同的映射规则决定了不同的LVM存储模型。LVM支持多个PV 的stripe和mirror。
LVM最大的特点就是可以对磁盘进行动态管理，因为逻辑卷的大小是可以动态调整的，而且不会丢失现有的数据，如果我们增加了硬盘也不会改变现有的上层逻辑卷。

---

##LVM优缺点

优点：
>* 文件系统可以跨多个磁盘，因此文件系统大小不会受物理磁盘的限制。
>* 可以在系统运行的状态下动态的扩展文件系统的大小。
>* 可以增加新的磁盘到LVM的存储池中。
>* 可以以镜像的方式冗余重要的数据到多个物理磁盘。
>* 可以方便的导出整个卷组到另外一台机器。

缺点：
>* 在从卷组中移除一个磁盘的时候必须使用reducevg命令（这个命令要求root权限，并且不允许在快照卷组中使用）。
>* 当卷组中的一个磁盘损坏时，整个卷组都会受到影响。
>* 因为加入了额外的操作，存贮性能受到影响

---

##LVM的名词解析

**PV(physical volume)**
物理卷在逻辑卷管理系统最底层，可为整个物理硬盘或实际物理硬盘上的分区。它只是在物理分区中划出了一个特殊的区域，用于记载与LVM相关的管理参数。 
**VG(volume group)**
卷组建立在物理卷上，一卷组中至少要包括一物理卷，卷组建立后可动态的添加卷到卷组中，一个逻辑卷管理系统工程中可有多个卷组。
**LV(logical volume)**
逻辑卷建立在卷组基础上，卷组中未分配空间可用于建立新的逻辑卷，逻辑卷建立后可以动态扩展和缩小空间。
**PE(physical extent)**
物理区域是物理卷中可用于分配的最小存储单元，物理区域大小在建立卷组时指定，一旦确定不能更改，同一卷组所有物理卷的物理区域大小需一致，新的pv加入到vg后，pe的大小自动更改为vg中定义的pe大小。
**LE(logical extent)**
逻辑区域是逻辑卷中可用于分配的最小存储单元，逻辑区域的大小取决于逻辑卷所在卷组中的物理区域的大小。由于受内核限制的原因，一个逻辑卷（Logic Volume）最多只能包含65536个PE（Physical Extent），所以一个PE的大小就决定了逻辑卷的最大容量，4 MB(默认) 的PE决定了单个逻辑卷最大容量为 256 GB，若希望使用大于256G的逻辑卷，则创建卷组时需要指定更大的PE。在Red Hat Enterprise Linux AS 4中PE大小范围为8 KB 到 16GB，并且必须总是 2 的倍数。

将各物理磁盘或分区初始化为PV（physical volume，物理卷），这一阶段可使用的命令为pvcreate、pvremove、pvscan、pvdisplay（pvs）
```vim
pvcreate：创建物理卷
  用法：pvcreate [option] DEVICE
      -f：强制创建逻辑卷，不需用户确认
      -u：指定设备的UUID
      -y：所有问题都回答yes
pvscan：扫描当前系统上的所有物理卷
  用法：pvscan [option]
      -e：仅显示属于输出卷组的物理卷
      -n：仅显示不属于任何卷组的物理卷
      -u：显示UUID
pvdisplay：显示物理卷的属性
  用法：pvdisplay [PV_DEVICE]
pvremove：将物理卷信息删除，使其不再被视为一个物理卷
  用法：pvremove [option] PV_DEVICE
      -f：强制删除
      -y：所有问题都回答yes
```

创建VG（volume group，卷组）。卷组将多个物理卷整合起来（屏蔽了底层细节），并划分PE（physical extend）。PE是物理卷中的最小存储单元，有点类似于文件系统中的block，PE大小可指定，默认为4M。这一阶段用到的命令有vgcreate、vgscan、vgdisplay、vgextend、vgreduce
```vim
vgcreate：创建卷组
  用法：vgcreate [option] VG_NAME PV_DEVICE
  选项：
      -s：卷组中的物理卷的PE大小，默认为4M
      -l：卷组上允许创建的最大逻辑卷数
      -p：卷级中允许添加的最大物理卷数
  例 vgcreate -s 8M myvg /dev/sda5 /dev/sda6
vgscan：查找系统中存在的LVM卷组，并显示找到的卷组列表
vgdisplay：显示卷组属性
  用法：vgdisplay [option] [VG_NAME]
  选项：
      -A：仅显示活动卷组的信息
      -s：使用短格式输出信息
vgextend：动态扩展LVM卷组，它通过向卷组中添加物理卷来增加卷组的容量
  用法：vgextend VG_NAME PV_DEVICE
  例 vgextend myvg /dev/sda7
vgreduce：通过删除LVM卷组中的物理卷来减少卷组容量，不能删除LVM卷组中剩余的最后一个物理卷
  用法：vgreduce VG_NAME PV_DEVICE
vgremove：删除卷组，其上的逻辑卷必须处于离线状态
  用法：vgremove [-f] VG_NAME
  -f：强制删除
vgchange：常用来设置卷组的活动状态
  用法：vgchange -a n/y VG_NAME
  -a n为休眠状态，休眠之前要先确保其上的逻辑卷都离线；
  -a y为活动状态
```

在卷组上创建LV（logical volume，逻辑卷）。为了便于管理，逻辑卷对应的设备文件保存在卷组目录下，为/dev/VG_NAME/LV_NAME。LV中可以分配的最小存储单元称为LE（logical extend），在同一个卷组中，LE的大小和PE是一样的，且一一对应。这一阶段用到的命令有lvcreate、lvscan、lvdisplay、lvextend、lvreduce、lvresize
```vim
lvcreate：创建逻辑卷或快照
  用法：lvcreate [选项] [参数]
  选项：
      -L：指定大小
      -l：指定大小（LE数）
      -n：指定名称
      -s：创建快照
      -p r：设置为只读（该选项一般用于创建快照中）
注：使用该命令创建逻辑卷时当然必须指明卷组，创建快照时必须指明针对哪个逻辑卷 
  例 lvcreate -L 500M -n mylv myvg
lvscan：扫描当前系统中的所有逻辑卷，及其对应的设备文件
lvdisplay：显示逻辑卷属性
  用法：lvdisplay [/dev/VG_NAME/LV_NAME]
lvextend：可在线扩展逻辑卷空间
  用法：lvextend -L/-l 扩展的大小 /dev/VG_NAME/LV_NAME  
  选项：
      -L：指定扩展（后）的大小。例如，-L +800M表示扩大800M，而-L 800M表示扩大至800M
      -l：指定扩展（后）的大小（LE数）
  例 lvextend -L 200M /dev/myvg/mylv
lvreduce：缩减逻辑卷空间，一般离线使用
  用法：lvexreduce -L/-l 缩减的大小 /dev/VG_NAME/LV_NAME  
  选项：
      -L：指定缩减（后）的大小
      -l：指定缩减（后）的大小（LE数）
  例 lvreduce -L 200M /dev/myvg/mylv
lvremove：删除逻辑卷，需要处于离线（卸载）状态
  用法：lvremove [-f] /dev/VG_NAME/LV_NAME

  -f：强制删除
```

---

##文件系统的扩展和缩减

文件系统在创建时是分成多个块组（block group）的，因此文件系统的增减实际上就是以增减块组的方式实现的。在linux中，ext系列格式的文件系统是可以扩展和缩减的，而xfs格式的目前只能扩展
**扩展文件系统**
```vim
先确定扩展的目标大小，并确保对应卷组中有足够的空闲空间可用。如果不够，可先通过vgextend命令扩大卷组容量
# vgextend myvg /dev/sda7
扩展逻辑卷
# lvextend -L 4G /dev/myvg/mylv 
扩展文件系统
resize2fs为ext系列文件系统大小的调整工具用法为：resize2fs 文件系统所对应的设备文件名 [大小]
# resize2fs /dev/myvg/mylv
```

**缩减很危险，要离线**
```vim
先确定缩减后的目标大小，并确保对应的目标逻辑卷大小足够容纳原有数据
先卸载文件系统，并要执行强制检测
# e2fsck -f
缩减文件系统
#resize2fs DEVICE        例如，resize2fs /dev/myvg/mylv 3G 缩减至3G
缩减逻辑卷
# lvreduce -L 3G /dev/myvg/mylv
注：当逻辑卷有快照时是不能扩展和缩减的，除非先将快照删除.
```

---

##snapshot（快照）
snapshot是像照相一样将当前的系统信息保存下来。
当创建一个snapshot的时候，仅拷贝原始卷里数据的元数据(meta-data)。创建的时候，并不会有数据的物理拷贝，因此snapshot的创建几乎是实时的，当原始卷上有写操作执行时，snapshot跟踪原始卷块的改变，这个时候原始卷上将要改变的数据在改变之前被拷贝到snapshot预留的空间里，因此这个原理的实现叫做写时复制(copy-on-write)。
在写操作写入块之前，原始数据被移动到 snapshot空间里，这样就保证了所有的数据在snapshot创建时保持一致。而对于snapshot的读操作，如果是没有修改过的块，那么会将读操作直接重定向到原始卷上，如果是已经修改过的块，那么就读取拷贝到snapshot中的块。
```vim
创建快照卷：
lvcreate [选项] [参数]
选项： 
-L：指定大小
-l：指定大小（LE数）
-n：指定名称
-s：创建快照
-p r：设置为只读
例如 lvcreate -s -L 512M -n mylv_snap -p r /dev/myvg/mylv # 针对逻辑卷mylv创建大小为512M的只读快照
注：创建快照前需将针对的逻辑卷临时改为只读，创建完毕后再改为读写，例如
- 创建快照前：mount -o remount,ro /dev/myvg/mylv /data
- 创建快照后：mount -o remount,rw /dev/myvg/mylv /data
```


