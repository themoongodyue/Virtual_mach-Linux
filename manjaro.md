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


