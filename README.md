# CachyOS 系统（KDE桌面）安装后配置指南

本文档记录了本人的CachyOS系统（KDE桌面环境）安装后的常用配置步骤，帮助本人快速搭建完整可用的桌面环境。配置内容包括输入法、字体、主题美化、办公软件等常用功能。

---

## 添加[archlinuxcn]源

在`/etc/pacman.conf`文件末尾添加两行：

```
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

然后安装`archlinuxcn-keyring`包以导入 GPG key。

```bash
pacman -Sy archlinuxcn-keyring
```

---

## 输入法配置

安装配置Fcitx5输入法框架，支持中文输入(我使用的是Rime)：

```bash
sudo pacman -S fcitx5 fcitx5-rime fcitx5-gtk fcitx5-qt fcitx5-configtool
```

最近发现了一个很不错的Rime拼音输入方案——万象拼音，基于白霜词库并可使用声调筛选候选词，其Pro版本支持多种双拼方案及多种辅助码方案，使用下来很符合我的个人习惯，现已在Linux/Android/MacOS三端都用上了。

该输入方案可以从AUR或者archlinuxcn源安装：

```bash
# 安装Pro版本的小鹤双拼方案+墨奇辅助码方案
sudo pacman -S rime-wanxiang-pro-flypy rime-wanxiang-pro-data-moqi-fuzhu
# 若仅保留万象拼音Pro方案，可以直接将wanxiang_pro_suggestion.yaml复制为default.custom.yaml，再作简单修改
cp /usr/share/rime-data/wanxiang_pro_suggestion ~/.local/share/fcitx5/rime/default.custom.yaml
```

### Fcitx环境变量设置
1. 在KDE设置中将虚拟键盘设置成"Fcitx 5 Wayland 启动器"
2. 编辑`/etc/environment`，添加一行：
   ```
   XMODIFIERS=@im=fcitx
   ```

### 解决GTK3应用输入法兼容性问题
部分GTK应用(如VSCode/Chromium)使用Fcitx5输入法时可能出现漏字母问题，解决方案：

1. 编辑 `~/.config/gtk-3.0/settings.ini` 添加：
   ```
   gtk-im-module=fcitx
   ```
2. 编辑 `~/.gtkrc-2.0` 添加：
   ```
   gtk-im-module="fcitx"
   ```

---

## 字体安装

安装常用中文字体和编程字体，确保系统显示效果良好：

```bash
sudo pacman -S noto-fonts-cjk noto-fonts-extra noto-fonts-emoji \
adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts \
wqy-microhei wqy-microhei-lite wqy-bitmapfont wqy-zenhei \
ttf-arphic-ukai ttf-arphic-uming ttf-jetbrains-mono-nerd \
ttf-roboto ttf-fira-code adobe-source-code-pro-fonts
```

### KDE字体设置建议
- 界面字体（使用调整所有字体）："Noto Sans CJK SC"，13pt
- 等宽字体："JetBrainsMono Nerd Font Mono"
- 启用抗锯齿处理
- 次像素渲染方式：关闭
- 字体轮廓微调：轻微

> 字体配置参考文档：[Arch Linux Font Improvement Guide](https://github.com/davgar99/arch-linux-font-improvement-guide)

---

## 主题美化

安装Orchis主题套件，打造美观统一的桌面环境，使用默认的微风主题也是可以的：

```bash
paru -S plasma6-themes-orchis-kde-git
sudo pacman -S kvantum tela-circle-icon-theme-all gtk-engines gtk-engine-murrine orchis-theme vimx-cursors
```

### 主题设置步骤
1. 全局主题改为Orchis
2. 应用程序外观设置为kvantum
3. Gnome/GTK外观为Orchis
4. 在Kvantum管理器中将主题切换成Orchis
5. 光标改为Vimix Cursors
6. 图标改为Tela circle系列主题

### 窗口特效设置
在"窗口管理-桌面特效"中勾选：
- 窗口惯性晃动
- 窗口透明度  
- 对话框显隐过渡

---

## 指纹解锁支持

[指纹解锁配置参考文档](https://blog.ucatch.me/post/archlinux-use-fingerprint)

1. 固件更新
```bash
sudo pacman -S fwupd
fwupdmgr get-devices
fwupdmgr refresh --force
fwupdmgr get-updates
fwupdmgr update
```

2. 检查指纹识别设备
```bash
sudo pacman -S usbutils
lsusb  # 检查是否有Fingerprint Reader相关设备
```

3. 安装指纹识别模块
```bash
sudo pacman -S fprintd imagemagick
paru -S pam-fprint-grosshack  # 支持同时验证指纹和密码
```

4. 参考上述文档配置指纹权限

---

## 办公软件安装

### WPS Office中文版

CachyOS 系统官方仓库有`wps-office`，但是该包对中文字体的支持较差，推荐安装AUR的`wps-office-cn`

```bash
paru -S wps-office-cn wps-office-mui-zh-cn wps-office-fonts ttf-wps-fonts
# wps-office-cn 主软件包
# wps-office-mui-zh-cn 中文界面，可以在安装完wps-office-cn后，直接使用 pacman -U ~/.cache/paru/clone/wps-office-cn/wps-office-mui-* 安装，减少二次构建的时间
# wps-office-fonts 方正中文字体字库
# ttf-wps-fonts 常用的西文、符号等基础字体字库
```

### WPS的输入法支持配置

默认情况下，安装完`wps-office-cn`之后，可以在程序界面使用Fcitx5输入法输入中文，但在文档编辑里面则不支持Fcitx5输入法，需要修改desktop文件，配置应用的环境变量，使其能够识别Fcitx5输入法。

```bash
mkdir -p ~/.local/share/applications
cp /usr/share/applications/wps-office*.desktop ~/.local/share/applications/
cd ~/.local/share/applications
ls -1 wps-office*.desktop |xargs sed -i 's/Exec=/Exec=env XMODIFIERS="@im=fcitx" GTK_IM_MODULE="fcitx" QT_IM_MODULE="fcitx" SDL_IM_MODULE=fcitx GLFW_IM_MODULE=ibus /'
update-desktop-database ~/.local/share/applications/ # 更新应用程序菜单
```

### Markdown编辑器
```bash
sudo pacman -S ghostwriter
```

---

## 其他实用软件

### 浏览器
```bash
sudo pacman -S zen-browser-bin
```

### Steam++ (Watt Toolkit)，网络加速支持Github网页加载
```bash
paru -S watt-toolkit-bin

# 证书配置(先运行一遍并点击网络加速-一键加速)
sudo chmod a+w /etc/hosts
sudo trust anchor --store ~/.local/share/Steam++/Plugins/Accelerator/SteamTools.Certificate.cer

# 解决证书失败的问题(或者点击一键加速失败的问题)
rm -r ~/.pki/nssdb  # 删除旧的证书数据库
mkdir -p ~/.pki/nssdb  # 创建新的证书数据库目录
chmod 700 ~/.pki/nssdb  # 设置适当权限
# 以下命令会创建一个新的证书数据库，并要求设置密码。完成后，重新导入证书即可
certutil -d sql:$HOME/.pki/nssdb -N  
sudo update-ca-trust  # 更新系统证书信任库
```

### EasyConnect VPN连接支持

```bash
paru -S easyconnect
```

使程序能记住当前用户的设置

```bash
echo "{}" > "~/setting_$(whoami).json"
sudo mv "~/setting_$(whoami).json" /usr/share/sangfor/EasyConnect/resources/conf/
sudo chown $(whoami):$(whoami) /usr/share/sangfor/EasyConnect/resources/conf/setting_$(whoami).json
```

若出现系统托盘图标变成浮动窗口，是由于`/usr/share/sangfor/EasyConnect/resources/conf/easy_connect.json`的`sys_type`的值变为`linux`了，需要修改为`neokylin`：
```bash
sudo set -i 's/"sys_type": ".*"/"sys_type": "neokylin"/' /usr/share/sangfor/EasyConnect/resources/conf/easy_connect.json
```

### 影音软件
```bash
sudo pacman -S feeluown-full osdlyrics vlc
```

### 腾讯系软件
```bash
paru -S linuxqq-appimage wechat-appimage wemeet-bin
```
> 注意: 不要安装`appimagelauncher`，否则微信无法安装成功

### 电子书阅读器
```bash
sudo pacman -S readest 
# 或 paru -S koodo-reader-bin，本人选择readest，因为官方仓库自带，方便升级
```

---

## 开发环境配置

```bash
sudo pacman -S code nodejs npm bun-bin
```

---

## 磁盘管理工具
```bash
sudo pacman -S gparted ntfs-3g smartmontools nvme-cli
```

---

## 虚拟机环境
```bash
sudo pacman -S virt-manager virt-viewer bridge-utils openbsd-netcat qemu-desktop

# Intel GVT-g虚拟显卡支持(仅限Intel显卡用户)
sudo pacman -S libva-utils intel-gpu-tools
paru -S gvtg_vgpu-git

# 用户组设置(使当前用户有权限管理虚拟机)
sudo usermod -aG libvirt $(whoami)
sudo usermod -aG kvm $(whoami)

# 启动libvirtd守护进程
sudo systemctl enable --now libvirtd.service  # 启用并立即启动服务

# 远程桌面支持
sudo pacman -S remmina freerdp
```

### NAT网络配置
```bash
sudo virsh net-start default  # 启动默认网络
sudo virsh net-autostart default  # 设置默认网络开机自启
echo 'firewall_backend = "iptables"' |sudo tee -a /etc/libvirt/network.conf  # 设置防火墙后端
sudo systemctl enable --now iptables.service  # 启用iptables服务
```

> 虚拟机Hooks配置参考：
> - [qemu-hooks脚本](https://github.com/PassthroughPOST/VFIO-Tools/blob/master/libvirt_hooks/qemu)
> - [hooks脚本参考](https://github.com/Thanatoslayer6/hd7730-singlegpu-passthrough)

---

### 系统启动参数持久化

对于使用systemd-boot引导的系统，修改`/boot/loader/entries/xxx.conf`的内核启动参数后，系统更新时可能会被覆盖。解决方案：

修改`/etc/sdboot-manage.conf`，在`LINUX_OPTIONS=`行中加入需要持久化的启动参数。
