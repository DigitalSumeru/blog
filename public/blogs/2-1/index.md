# DS218+更新DS923+

## 一、群晖关闭硬盘验证&开启m2存储空间

　　ssh登录到群晖后，打开文件：

```
vi /etc.defaults/synoinfo.conf
```

　　开启m2存储池支持

```
support.m2.pool = "no"  // => 这里的no改成 yes
```

　　关闭硬盘验证

```
support_disk_compatibility="yes" // => 这里的yes改成no
```

　　重启群晖

```
reboot
```

## 二、储存空间不兼容，不受当前DSM版本支持错误

　　（1）下载脚本

```
https://github.com/007revad/Synology_HDD_db
https://github.com/007revad/Synology_HDD_db/archive/refs/tags/v3.6.111.zip
```

　　（2）、脚本作用：
将您的 SATA 或 SAS 硬盘和固态硬盘以及 SATA 和 NVMe M.2 硬盘添加到 Synology 的兼容硬盘数据库，包括您的 Synology M.2 PCIe 卡和扩展单元数据库。

　　它还具有还原选项，可撤消脚本所做的所有更改。

　　（3）、下载、放入群晖中解压：
打开链接，下载最新版 （zip）文件

![image-20240606160243163](https://cdn.sa.net/2024/06/06/UaY8lFCLWJpD5ER.png)

　　随便在群晖储存空间中**解压**

```
/volume1/homes/WangGang/ssh/Synology_HDD_db-3.5.90（直接右键属性-位置 复制）
```

​![image-20240606160501857](https://cdn.sa.net/2024/06/06/NtFWcOeI8pwGvdi.png)​

　　（4）在SSH工具上登录并提升权限
Win在SSH命令窗口需要输入你当前的用户名，回车后输入密码（密码不会显示）

　　Mac在终端界面只输入密码（密码不会显示）

　　提升权限，输入

```
 sudo -i 
```

　　在输入当前账户的密码（密码不会显示），回车

　　（5）赋权
输入

```
cd /volume1/homes/WangGang/ssh/Synology_HDD_db-3.5.94
```

　　注意：cd 后面是你存放此脚本的位置

　　然后对 syno_hdd_db.sh 进行赋权

　　输入

```
chmod +x syno_hdd_db.sh
```

　　查看脚本相关的运行参数
输入

```
./syno_hdd_db.sh -help
```

　　以下是脚本参数

```
-s, --showedits Show edits made to _host db and db.new file(s)

-n, --noupdate Prevent DSM updating the compatible drive databases

-m, --m2 Don't process M.2 drives

-f, --force Force DSM to not check drive compatibility

-r, --ram Disable memory compatibility checking (DSM 7.x only) and set max memory to amount of installed memory

-w, --wdda Disable WD WDDA

--restore Undo all changes made by the script

--autoupdate=AGE Auto update script (useful when script is scheduled)

AGE is how many days old a release must be before auto-updating. AGE must be a number: 0 or greater

-h, --help Show this help message

-v, --version Show the script version
```

　　我们只用 -f 强制群晖不检查设备的驱动器兼容性

　　输入

```
./syno_hdd_db.sh -f
```

　　出现

```
DSM successfully checked disk compatibility
```

　　说明脚本已经运行完成

　　输入

```
reboot
```

　　重启群晖

　　重启后你就会在群晖-储存空间管理器-储存空间中看到 M2 SSD 储存空间不再报错

　　（6）找出nvme设备

```
ls /dev/nvme*
```

　　一般情况会显示 /dev/nvme0n1 和 /dev/nvme1n1，下面以/dev/nvme0n1为例，/dev/nvme1n1同理，只需要更改数字即可

![查看nvme设备信息](https://cdn.sa.net/2024/06/06/nFC2EmlgjcrNM4Q.png)

　　（7）查看磁盘信息

```
fdisk -l /dev/nvme0n1
```

![查看磁盘信息](https://cdn.sa.net/2024/06/06/QIbPdjCfWmkJrHG.png)

　　（8）分区

```
synopartition --part /dev/nvme0n1 12

在出现warning字样时输入Y
```

![](https://cdn.sa.net/2024/06/06/lwpHTbUoQ9MR872.png)

　　（9）查看分区布局

```
fdisk -l /dev/nvme0n1
```

![查看分区布局](https://cdn.sa.net/2024/06/06/X1APGQplrFj6YEb.png)

　　（10）查看现有阵列

```
cat /proc/mdstat
```

![查看现有阵列](https://cdn.sa.net/2024/06/06/4shwdqkueOx8AFI.png)

　　（11）确定阵列序号

　　第六步查看现有阵列已经看到目前群晖已经有几个阵列了，选取一个系统没有的阵列序号，这里以md5为例

　　（12）-1

　　**创建阵列一——btrfs文件系统（单盘模式）——————适合做存储空间**

```
mdadm --create /dev/md5 --level=1 --raid-devices=1 --force /dev/nvme0n1p3

mkfs.btrfs -f /dev/md5
```

　　![建立单盘](https://cdn.sa.net/2024/06/06/qwJQtuoLYZ7bVR8.png)建立单盘

　　![设定btrfs系统](https://cdn.sa.net/2024/06/06/SoGfU6MvxW8gyYR.png)设定btrfs系统

　　(12)-2

　　创建raid0阵列———————游戏数据盘

　　这里默认你的第二块盘也已经完成分区，如果没有请重复

```
mdadm --create /dev/md5 --level=0 --raid-devices=2 --force /dev/nvme0n1p3 /dev/nvme1n1p3
```

　　(12)-3

　　创建raid1阵列——————减低噪音

```
mdadm --create /dev/md5 --level=1 --raid-devices=2 --force /dev/nvme0n1p3 /dev/nvme1n1p3
```

　　![raid1](https://cdn.sa.net/2024/06/06/OFQxg5oHCidpBa8.png)raid1

```
reboot
```

　　重启生效。

　　（13)恢复存储空间

　　重启进入群晖后，系统会提示可以恢复存储池，进入存储管理器，按系统提示点击恢复即可使用。

　　![btrfs命令后续](https://cdn.sa.net/2024/06/06/l8OBrqiPgzLp1VG.png)btrfs命令后续

　　![raid1后续](https://cdn.sa.net/2024/06/06/4bwSuhIfBvimL2c.png)raid1后续

　　目前只有创建raid0阵列时会遇到具体修复办法是，进入群晖套件中心，打开SAN Manager，删除LUN，然后即可正常创建存储池。

　　（14）防止后续群晖更新非官方白名单失效，需要在计划任务中添加开机自动运行

　　点击控制面板-计划任务-新增-触发任务-用户自定义的脚本

![image-20240606162739225](https://cdn.sa.net/2024/06/06/e2blFWS54nqOKEr.png)

　　任务名称自定义 ，用户账号 root

![image-20240606162827346](https://cdn.sa.net/2024/06/06/ZqY3ePN6vUalxCs.png)

　　点击 任务设置，通知选项是可选项，如果需要邮件通知，可以选择

　　运行命令-用户自定义脚本

　　输入 syno_hdd_db.sh 的完整目录（找到存放的 syno_hdd_db.sh 位置，属性-位置，全部复制）

```
/volume1/homes/WangGang/ssh/Synology_HDD_db-3.5.90/syno_hdd_db.sh
```

![image-20240606162933906](https://cdn.sa.net/2024/06/06/aiWvFuhGgOLo71N.png)

　　确定，输入密码确认，提交完成

## 三、查看内存

　　通过终端执行

```
dmidecode -t memory
```

　　命令发现，Maximum Capacity: 64 GB

　　购买了2条海力士ECC 32G插上果然正常识别，同时ECC也是生效的：

![img](https://cdn.sa.net/2024/06/06/m2pqgkBrIH3GlVN.png)

## 三、修复群晖强制删除套件导致无法再安装

###### 解决方法：安装或者删除一个别的套件，再安装此套件即可。。。。。。折腾几个小时 找到的方案

　　原理应该是直接删除文件并没有更新套件列表，因为没有upgrade执行脚本导致安装错误，通过安装卸载别的套件即可更新修复。

#### PS：套件卸载后在系统的残留

　　首先：这些残留并不影响系统的运行或者有任何副作用！！！以下内容仅限**洁癖**查看。。。

　　举例 alist3 卸载后的残留文件位置：

```
/usr/syno/synoman/webman/3rdparty/alist3
/usr/syno/etc/packages/alist3
/volume1/@appdata/alist3 #某些DSM7有选择仅卸载时，配置会保留在此，下次安装可以恢复如初
/volume1/@apptemp/alist3
/volume1/@apphome/alist3
/volume1/@appshare/alist3
/volume1/@appconf/alist3
```

　　删除 alist3 的用户和用户组

```
synouser --del alist3
synogroup --del alist3
```

　　另：群晖全部用户列表：/etc/passwd 用户组列表：/etc/group ，不建议手贱动这俩文件

　　‍
