# 安装

https://arch.icekylin.online/prologue.html

https://archlinuxstudio.github.io/ArchLinuxTutorial/#/

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