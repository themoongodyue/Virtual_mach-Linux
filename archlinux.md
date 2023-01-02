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

# 还未进行的

## btrfs
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

