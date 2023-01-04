# 安装

https://arch.icekylin.online/prologue.html  基于btrfs
 
https://archlinuxstudio.github.io/ArchLinuxTutorial/#/  基于ext4

# gnome拓展的配置文件

1. /usr/share/gnome-shell/extensions/  #全局用户
2. ~/.local/share/gnome-shell/extensions #当前用户

修改其中的metadata.json

# aur的pkgbuild

进入到任一软件包构建文件夹，修改PKGBUILD文件然后运行
```bash
makepkg -si
```
即可完成构建

# 键盘相关
## Fn键的设置
$$
\begin{array}{|c|c|c|}
\hline \text { 值 } & \text { 含义 } & \text { 用途 } \\
\hline 0 & \text { disable } & \text { 废掉Fn键, 按了Fn也是F1-F12本身的功能 } \\
\hline 1 & \text { fkeyslast } & \text { 功能键F1-F12本身的功能靠后, 也就是默认一直按下Fn键 } \\
\hline 2 & \text { fkeysfirst } & \text { 默认使用F1-F12键本身的功能, 要用实现特殊功能需要先按Fn再按F1-F12 } \\
\hline
\end{array}
$$
```bash

    cd /etc/modprobe.d
    touch hid_apple.conf
    echo options hid_apple fnmode=2 > hid_apple.conf
    # 重启，永久生效

```
# 还未进行的

## **btrfs**
**btrfs一般不用其碎片整理功能\
在fstab中设置如下即可关闭碎片整理**
```bash
UUID=XXXXXXXX   /home   btrfs   noautodefrag,defaults 0 0
```
**btrfs文件系统的收缩**
```bash

    btrfs filesystem usage /test  #查看文件系统的使用情况
    btrfs filesystem resize -120G /test #缩小100G，但先多缩一点
    umount /test #卸载
    cfdisk /dev/sda1 #使用cfdisk将分区resize到小100G，这样会更精准
    mount /dev/sda1 /test #重新挂载
    btrfs filesystem resize max /test #扩容到最大，完成最初的收缩100G目的

```
**btrfs文件系统的--在线添加和删除块设备（硬盘）**
```bash
    mount /dev/sda4 /test
    btrfs device add /dev/sda5 /test #新添加的设备（sda5）不必格式化文件系统，只要类型是linux filesystem即可
    btrfs filesystem balance /test #进行负载均衡
    btrfs device remove /dev/sda5 /test #移除快设备
```
**btrfs的文件系统级磁盘阵列**\
在格式化文件系统时进行
```bash
    #例子: -L data 取名为data; -d raid10 数据块为raid10; -m raid1 元数据块为raid1(一般和数据块相同即可); -f 选取相应的分区
    mkfs.btrfs -L data -d raid10 -m raid1 -f /dev/sda1 /dev/sda2 /dev/sda3
    #挂载第一个即可
    mount /dev/sda1 /test
```
**btrfs子卷**\
表现上类似目录，但有自己的uuid，即可以看作已经挂载的分区\
可以用目录操作进行删除等操作，也有子卷的专属操作
```bash

    #创建/mnt/test子卷
    btrfs subvolume create /mnt/test
    # 创建 / 目录子卷
    btrfs subvolume create /mnt/@ #@的这种要单独挂载
    # 创建 /home 目录子卷
    btrfs subvolume create /mnt/@home 
    #@表示顶级子卷
```
**btrfs透明压缩**\
挂载时启用透明压缩
```bash

    #以zstd算法进行透明压缩存储
    mount -t btrfs -o compress=zstd /dev/sdxn /mnt
```
**btrfs快照功能**\
一般是给子卷拍快照
```bash

    #可读写快照
    btrfs subvolume snapshot /mnt/test /mnt/.test_01
    #只读快照
    btrfs subvolume snapshot -r /mnt/test /mnt/.test_02
```
**btrfs文件系统克隆**\
利用只读快照完成
```bash

    #举例：只克隆根目录，根目录在sda2，要把它克隆到sdb1上并引导。
    # 只有根目录和boot目录的情况
    mount -t btrfs /dev/sdb1 /mnt
    btrfs subvolume snapshot -r / /.newroot #创建跟的只读快照.newroot
    btrfs send /.newroot | btrfs receive /mnt/ #发送到目标分区(sdb1)上
    btrfs send /.newroot | ssh root@ipaddress "btrfs receive /mnt/" #通过ssh远程发送快照
    btrfs subvolume snapshot /mnt/.newroot /mnt/.root #在目标分区上创建可读写快照
    mv /mnt/.root/* /mnt #将快照的内容复制到新的根目录里

    vim /mnt/etc/fstab #修改新的根目录的fstab的uuid
    vim /boot/grub/grub.cfg #修改grub的uuid（boot分区是单独挂载的不和/在一块）
```
**ext3/4离线转换成btrfs**\
可利用liveusb完成
```bash

    #以下操作在liveusb环境中进行
    umount /mnt #卸载以保证离线
    btrfs-convert /dev/sda2 #进行转换
    blkid #查看uuid
    mount /dev/sda2 /mnt #挂载来修改fstab的uuid
    vim /mnt/etc/fstab
    mount /dev/sda1 /mnt/boot #挂载boot分区修改grub.cfg的uuid
    vim /mnt/boot/grub/grub.cfg
```
