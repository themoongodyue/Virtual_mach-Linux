# 配置参考
[知乎](https://zhuanlan.zhihu.com/p/114296129)
[CSDN](https://blog.csdn.net/qq_44417226/article/details/109617395?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.no_search_link&spm=1001.2101.3001.4242.1)
[bilibili](https://www.bilibili.com/read/cv10524566)
# CUDA
https://www.cnblogs.com/bugxch/p/13833583.html
https://www.jianshu.com/p/c27f39da3254
**1.删除Bumblebee或者开源驱动**
 .方法一： 使用mhwd命令删除即可
  开源驱动：sudo mhwd -i pci video-nvidia
  删除Bumblebee就把video-nvidia改成系统设置-›硬件设定里Bumblebee驱动的名字

 .方法二：直接在硬件设定里右键选移除
**2.安装nvidia私有闭源驱动**
 .方法一
  在系统设置-›硬件设定里直接右键安装下面的驱动中的一个
  ```
  video-nvidia-340xx
  video-nvidia-390xx
  video-nvidia-418xx
  video-nvidia-430xx
  video-nvidia-435xx
  video-nvidia-440xx
  ```
 .方法二
  ```shell
  sudo mhwd -a pci nonfree 0300
  ```
  可能还要装依赖（如果有这些包，如果重启后nvidia-smi正常就不用装）！！现在还不能重启
  ```shell
   nvidia-prime
   lib32-opencl-nvidia
   nvidia-utils
   linux513-nvidia #根据内核版本（这个好像也是驱动）
   oprncl-nvidia
   lib32-nvidia-utils
   mhwd-nvidia #这个应该就是驱动 
  ```
**3. 安装依赖**
  ```shell
  sudo pacman -S linuxXXX-headers acpi_call-dkms xorg-xrandr xf86-video-intel git
  #linuxXXX-headers的名字换成你自己内核版本的名字，系统设置-›内核里查看正在运行的内核，我是linux513-headers
  ```
**4.载入模块**
 ``` shell
  sudo modprobe acpi_call
 ```
**5.安装**
 ```shell
  git clone https://github.com/dglt1/optimus-switch-sddm.git
  cd ~/optimus-switch-sddm
  chmod +x install.sh
  sudo ./install.sh
 ```
**6.切换Nvidia Prime和Intel核显模式**
 ```shell
  #切换Intel核显
  sudo set-intel.sh
  #切换Nvidia Prime
  sudo set-nvidia.sh
 ```
### **若安装驱动时用了方法二（没有指定驱动版本），会自动安装最新版而且会随着pacman -Syyu自动更新，最好要阻止驱动更新**
### 不成熟的办法如下
 ```shell
 sudo nano /etc/pacman.conf
 ```
 然后在其中IgnorePkg=要阻止的包
 ```shell
IgnorePkg   = nvidia-prime
IgnorePkg   = lib32-opencl-nvidia
IgnorePkg   = nvidia-utils
IgnorePkg   = linux513-nvidia
IgnorePkg   = oprncl-nvidia
IgnorePkg   = lib32-nvidia-utils
IgnorePkg   = mhwd-nvidia
IgnorePkg   = egl-wayland # 大概是这些
 ```

# timeshift
https://zhuanlan.zhihu.com/p/346602946
# 开机卡死
开机shift进入grub按e删除quit若发现是
**双显卡切换问题**
则安装optimus-switch-sddm(参考前面的b站教程)
# 关机开机卡顿
开源显卡驱动，或者引导，或者电源管理方案问题
参考如下
https://blog.csdn.net/Tangcuyuha/article/details/80298500

重点如下
编辑/etc/default/grub文件，再该文件下查找GRUB_CMDLINE_LINUX=”“一行，修改为：
```bash
GRUB_CMDLINE_LINUX="reboot=efi"//我的本子是efi引导
GRUB_CMDLINE_LINUX="reboot=bios"    
GRUB_CMDLINE_LINUX="reboot=acpi"
GRUB_CMDLINE_LINUX="reboot=pci"
```
或者如下
```bash
sudo vim /etc/systemd/system.conf
DefaultTimeoutStartSec=10s
DefaultTimeoutStopSec=10s
DefaultRestartSec=100ms
###保存
sudo systemctl daemon-reload#更改生效
```
# 将/opt /usr /home /var挂载到其他盘
1. /home /opt /var 可以根据如下教程进行\
[动态拓展参考教程](https://www.howtogeek.com/442101/how-to-move-your-linux-home-directory-to-another-hard-drive/)


2. **挂载/usr分区时建议使用u盘启动盘操作\
操作完后，在u盘环境下手动挂载四个分区然后manjaro-chroot进本机环境，然后执行如下操作**
```bash
nano /etc/fstab # 找到/usr分区的那一行，将<pass>的值设置成0。

nano /etc/mkinitcpio.conf #在HOOK中增加shutdown和usr。
#如下
HOOK="base udev autodetect modconf block filesystems keyboard shutdown usr fsck"

#最后别忘了执行创建ramdisk环境。
mkinitcpio -p linuxxxx #xxx是内核版本号

```
# 日常问题
## 软件库更新问题
```bash
sudo pacman -Syy
错误：GPGME 错误：无数据
错误：未能同步所有数据库（未预期的错误）
```
**主要是AUR和官方库冲突造成的（pamca里面AUR的确认没跟官方库冲突再更新，尽量少用AUR）**

执行如下即可：
```bash
sudo rm -R /var/lib/pacman/sync
```
## 键盘映射（Xmodmap ）
使用是前\
xmodmap 不提供恢复初始化到功能，所以在使用如下指令备份map表，防止map出错
```bash
xmodmap -pke > /etc/X11/.Xmodmap.bak
```
如果出错导致键盘不能正常使用，可以重启。如果是外接键盘，重新插拔即可恢复
**映射**：
1.
```bash
vim ~/.xmodmap
```
2.在其中加入映射关系，然后
3.保存，执行
```bash
xmodmap ~/.xmodmap
```
## FTP服务
下载vsftp
```bash
sudo pacman -S vsftpd
```
配置服务
```bash
sudo vim /etc/vsftpd.conf
###
anonymous_enable=NO  ##拒绝匿名
local_enable=YES  ##允许本机用户（及非专门用户）
write_enable=YES  ## 允许写
local_umask=022
```
重启服务
```bash
sudo systemctl restart vsftpd
```
创建专门用户
```bash
useradd -g ftp -d /home/userftp -s /sbin/nologin userftp
passwd userftp
```
配置用户
```bash
sudo vim /etc/vsftpd.conf
###
nopriv_user=userftp
```
重启服务\
排查问题
1、查看/etc/ftpusers ，确保账号没有在这个文件内。\
2、修改/etc/pam.d/vsftpd\
将auth required pam_shells.so修改为->auth required pam_nologin.so 即可\
3、重启vsftpd

1、chmod 777 /home/userftp
2、chown -R root.root /home/userftp

## 配置Qv2ray
到Qv2ray官方github仓库：https://github.com/Qv2ray/Qv2ray \
下载运行appimage版的qv2ray（由于manjaro的仓库原因）\
安装v2ray内核
    yay -S v2ray


https://www.buptstu.cn/2021/02/07/Manjaro%E9%85%8D%E7%BD%AEQv2ray-SSR/



####
# 建议安装顺序
软件源\
archcn源\
aru（yay）\
解决开机卡死问题\
输入法fcitx5\
()[https://blog.csdn.net/m0_47627464/article/details/113790309]\
edge\
anaconda\
美化
