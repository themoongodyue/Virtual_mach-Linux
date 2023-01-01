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
