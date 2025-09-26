# cachyos-install-notes
CachyOS系统安装后的配置记录

# 安装输入法

```bash
sudo pacman -S fcitx5 fcitx5-rime fcitx5-gtk fcitx5-qt fcitx5-configtool rime-double-pinyin
```

- (KDE设置)将虚拟键盘设置成"Fcitx 5 Wayland 启动器"

- 编辑`/etc/environment`，添加一行 `XMODIFIERS=@im=fcitx`

- 修复VSCode/Chromium等Gtk3软件使用fcitx5输入拼音出现漏字母的问题：
	- 编辑 `~/.config/gtk-3.0/settings.ini` 添加一行 `gtk-im-module=fcitx`
	- 编辑 `~/.gtkrc-2.0` 添加一行 `gtk-im-module="fcitx"`

# 安装字体

```bash
sudo pacman -S noto-fonts-cjk noto-fonts-extra noto-fonts-emoji adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts wqy-microhei wqy-microhei-lite wqy-bitmapfont wqy-zenhei ttf-arphic-ukai ttf-arphic-uming ttf-jetbrains-mono-nerd ttf-roboto ttf-fira-code adobe-source-code-pro-fonts
```

(KDE设置)批量将字体调整成"Noto Sans CN"，大小为13pt，等宽字体设置成"Jetbrains Mono Nerd Font"，启用抗锯齿处理，次像素渲染方式-RGB，轮廓微调-轻微

> 字体配置参考文档： https://github.com/davgar99/arch-linux-font-improvement-guide

# 主题美化

```bash
paru -S plasma6-themes-orchis-kde-git
sudo pacman -S kvantum tela-circle-icon-theme-all gtk-engines gtk-engine-murrine orichis-theme vimx-cursors
```
全局主题改为Orchis，应用程序外观设置为kvantum，Gnome/GTK外观为Orchis，Kvantum管理器中将主题切换成Orchis，光标改为Vimix Cursors，图标改为Tela circle系列主题

# 安装指纹解锁支持

[参考文档](https://blog.ucatch.me/post/archlinux-use-fingerprint)

```bash
# 安装固件管理软件包
sudo pacman -S fwupd
# 查看电脑相关硬件设备
fwupdmgr get-devices
# 刷新设备版本状态
fwupdmgr refresh --force
# 获取更新
fwupdmgr get-updates
# 安装更新
fwupdmgr update
```

```bash
# 先检查电是否也指纹设备
## 是否有 Fingerprint Reader 或相关字样
sudo pacman -S usbutils
lsusb

# 安装指纹模块包
sudo pacman -S fprintd imagemagick

# 同一时间可以对指纹或密码做验证
## 而非密码不通过再去验证指纹以及对立情况
paru -S  pam-fprint-grosshack
```

# 安装办公软件

```bash
sudo pacman -S wps-office
# 安装markdown编辑器
sudo pacman -S ghostwriter
```

# 安装zen浏览器

```bash
sudo pacman -S zen-browser-bin
```

# 安装Steam++，支持连接github

```bash
paru -S watt-toolkit-bin

# 配置
sudo chmod a+w /etc/hosts
sudo trust anchor --store ~/.local/share/Steam++/Plugins/Accelerator/SteamTools.Certificate.cer
```

# 安装EasyConnect

```bash
paru -S easyconnect
# 使程序能记住当前用户的设置
echo "{}" > "~/setting_$(whoami).json"
sudo mv "~/setting_$(whoami).json" /usr/share/sangfor/EasyConnect/resources/conf/
sudo chown $(whoami):$(whoami) /usr/share/sangfor/EasyConnect/resources/conf/setting_$(whoami).json
# 若出现系统托盘图标变成浮动窗口，编辑/usr/share/sangfor/EasyConnect/resources/conf/easy_connect.json，将sys_type的值改为neokylin
sudo set -i 's/"sys_type": ".*"/"sys_type": "neokylin"/' /usr/share/sangfor/EasyConnect/resources/conf/easy_connect.json
```

# 安装影音软件

```bash
sudo pacman -S feeluown-full osdlyrics vlc 
```

# 安装腾讯系软件

```bash
# QQ、微信、腾讯会议
paru -S linuxqq-appimage wechat-appimage wemeet-bin
```

> 注: 不要安装`appimagelauncher`，否则微信无法安装成功

# 安装电子书阅读软件

```bash
# Readest和Koodo可以二选一
sudo pacman -S readest
paru -S koodo-reader-bin
```

# 安装开发环境

```bash
# Code-OSS，NodeJS，Bun
sudo pacman -S code nodejs npm bun-bin 
```

# 安装磁盘工具

```bash
sudo pacman -S gparted ntfs-3g
```

# 安装虚拟机环境

```bash
sudo pacman -S virt-manager virt-viewer bridge-utils openbsd-netcat qemu-desktop
# intel GVT-g 虚拟显卡支持
sudo pacman -S libva-utils intel-gpu-tools
paru -S gvtg_vgpu-git
# 添加到用户组
sudo usermod -aG libvirt $(whoami)
sudo usermod -aG kvm $(whoami)
sudo systemctl enable --now libvirtd.service
# 远程桌面支持
sudo pacman -S remmina freerdp
```

```bash
# NAT网络支持
sudo virsh net-start default
sudo virsh net-autostart default
echo 'firewall_backend = "iptables"' |sudo tee -a /etc/libvirt/network.conf
sudo systemctl enable --now iptables.service
```

> qemu-hooks脚本: https://github.com/PassthroughPOST/VFIO-Tools/blob/master/libvirt_hooks/qemu
> 
> hooks脚本参考：https://github.com/Thanatoslayer6/hd7730-singlegpu-passthrough
