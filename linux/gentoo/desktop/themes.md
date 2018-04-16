#  设置主题
 - AUTHOR: xinzhiniepan
 - DATE: 2018-04-16 14:20

## 主题设置app
lxappearance

## 图标和鼠标设置
[address](https://www.gnome-look.org/browse/cat/107/ord/latest/ "下载地址")
解压到~/.icons文件夹中

配置一般的主题
创建文件夹
```
mkdir -p .icons/default
```
设置主题(主要设置鼠标主题)
```
# This file is written by LXAppearance. Do not edit.
[Icon Theme]
Name=Default
Comment=Default Cursor Theme
#Inherits=capitaine-cursors
#设置主题,oxy为主题名(注意大小写,一般都是主题文件夹的名字)
Inherits=oxy
```

配置gtk主题(主要设置图标)
创建gtk-3.0目录
```
mkdir p ~/.config/gtk-3.0
```
创建配置文件setting.ini
```
[Settings]
gtk-theme-name=Arc
gtk-icon-theme-name=Arc-OSX-D
gtk-font-name=Sans 10
gtk-cursor-theme-name=oxy
gtk-cursor-theme-size=0
gtk-toolbar-style=GTK_TOOLBAR_BOTH
gtk-toolbar-icon-size=GTK_ICON_SIZE_LARGE_TOOLBAR
gtk-button-images=1
gtk-menu-images=1
gtk-enable-event-sounds=1
gtk-enable-input-feedback-sounds=1
gtk-xft-antialias=1
gtk-xft-hinting=1
gtk-xft-hintstyle=hintfull
```
PS:一般来说一般的主题和gtk主题一致否则会出现多个主题
