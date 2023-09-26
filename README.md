# Arch Linux 安装简明流程

这是一篇为 **GPT/EFI 引导** 的电脑安装 Arch Linux（双系统）的中文简明流程，尽可能省略了可以省略的流程与文字以使得篇幅尽量短小，基本上基于 [Arch Wiki Installation guide](https://wiki.archlinux.org/title/installation_guide) 以及由联创团队 Android 组学长的这篇 [优秀的安装教程](https://www.viseator.com/2017/05/17/arch_install/)，特此表示感谢。

本文严格按照操作顺序进行编写，还请认真阅读每一步操作的说明以及注意事项。

> 说明：下文中所指教程均指此篇教程: [以官方Wiki的方式安装ArchLinux](https://viseator.github.io/2017/05/17/arch_install/)。



## 安装前准备

1. 大于 100G 的硬盘空余空间。
2. 一块储存空间至少 4G 的 U 盘。
3. 稳定，而不需要额外进行设备认证的有线/无线网络连接，如手机热点等。
4. 至少 3 小时~~或长达数星期~~的时间。
5. 基本的搜索引擎使用能力以及英语阅读能力。
5. ~~够闲~~





## 准备安装介质与磁盘分区

### 安装介质

此处默认你使用 Windows 系统，其他情况请参阅 [Arch Wiki](https://wiki.archlinux.org/title/USB_flash_installation_medium)。

1. 下载 Arch Linux 最新的系统镜像，在 [下载页面](https://archlinux.org/download/) 下方找到 China 的镜像源，如 `tuna.tsinghua.edu.cn`，下载文件名形如 `archlinux-2022.04.05-x86_64.iso` 的 iso 镜像文件。
2. 下载 [Rufus](https://rufus.ie/zh/)，解压即用的便携版即可。
3. 使用 Rufus 将 Arch Linux 镜像装载到 U 盘，所有参数保持默认不修改即可，如果 U 盘内存放有数据请先备份。

### 磁盘分区

> **Warning**
>
> 重要提示：如果你的 Windows 电脑为磁盘加上了 **Bitlocker 锁**，请务必解除后再进行任何磁盘操作，否则会为你带来巨大的不幸。

此处默认 Arch Linux 与原系统安装在同一块硬盘上，如果你需要在一块新硬盘上安装，你还需要确定或转换新硬盘的分区表为 GPT 格式，并新建一个 EFI 分区。具体的其他情况请查阅 Arch Wiki、[教程](https://www.viseator.com/2017/05/17/arch_install/) 或自行搜索。

1. 使用 Windows 自带的磁盘管理，你可以直接在开始菜单中搜索找到它，或者右键单击计算机，选择管理。
2. **压缩** 有空余空间的磁盘分区，分配一块空间给 Arch Linux，多多益善，最小不要小于 60G，如果你要使用 Arch Linux 作为你的主要开发环境，此分区建议不要小于 200G。~~（我分了一整块硬盘）~~
3. **不要** 在这里新建一个新分区，而是在进入 Arch Linux 安装程序后再进行这一步。



## BIOS 设置

不同品牌的电脑/主板进入 BIOS 设置的方法不一样，联想以及戴尔是在 **按下开机键出现品牌徽标时** 按 F2（或 Fn+F2），启动菜单是按下 F12，其他品牌请自行搜索。

各品牌 BIOS 设置界面各自不同，但需要做的事情大同小异。

1. 关闭 `Secure Boot`，Arch Linux 安装程序无法使用 `Secure Boot` 启动，你可以在完成安装之后再启用此功能。
2. 某些品牌（如戴尔）的电脑可能不会在其他系统中默认开启网卡，需要在设置中启用  `Enable UEFI Network Stack`。
3. 调整 BIOS 默认的启动顺序（`Boot Sequence` / `Boot Order`），检查是否有装载有 Arch Linux 的 U 盘，将其顺序调整到第一位（你也可以在计算机启动时手动进入启动菜单选择要启动的系统），保存 BIOS 设置并退出。

   > 说明：如果在这里找不到你的 U 盘（设备名形如 `ARCH_202204` ），说明可能你的设备开启了 `Secure Boot` 导致 BIOS 无法找到系统入口，保存 BIOS 设置退出后重新进行这一步即可。






## 开始安装 Arch Linux

完成了 BIOS 的设置后，重启计算机，我们应该进入到了 Arch Linux 的界面，选择第一个进入安装程序即可进入 `zsh` 命令行界面。





## 连接网络与更新系统时间

> 说明：此部分问题较多，请耐心使用控制变量法以及搜索引擎进行问题排查。



### 连接有线网络

不同型号的无线网卡的支持情况不同，若有条件推荐优先使用有线网网络进行连接，可以直接使用 USB 线将手机连接电脑使用手机的数据网络。

接入网线或手机进行有线网连接，并测试网络是否联通


```
ping www.baidu.com
```

> 使用 `Ctrl+C` 中止当前正在执行的命令





### 连接无线网络



#### 事前故障排除

解除可能出现的软硬件 block


```shell
rfkill unblock all
```

列出当前网络设备

```shell
ip link show
```

一般而言，无线网卡的名字默认为 `wlan0`，检查其状态，若为 `DOWN` 还需设置为 `UP`，*wlan0* 请替换成此处显示的网卡名

```
ip link set wlan0 up
```



执行以下命令

```
iwctl
```

会进入一个以 [iwd] 开头的命令行环境中，接着执行：

```
device list
```

会列出当前可用的所有网卡设备，一般而言，无线网卡的名字默认为 `wlan0`，接着执行下列命令进行无线网络的扫描：`wlan0` 请替换成此处显示的网卡名

```
station wlan0 scan
```

接着执行下列命令列出扫描到的网络：

```
station wlan0 get-networks
```

最后输入下列命令连接指定网络：`Wifi-SSID` 请切换成你想要连接的网络，输入密码回车即可连接成功。

```
station wlan0 connect Wifi-SSID
```

> 提示：单次或多次按下 `Tab` 可以补全或选择可能的选项，免去输入校对之苦

使用 `quit` 退出 `iwc`，测试网络是否联通

```
ping www.baidu.com
```

> 使用 `Ctrl+C` 中止当前正在执行的命令

> 提示：可以使用方向键的上、下键来查看曾经执行过的指令的历史记录



### 设置系统时间

```
timedatectl set-ntp true
```

操作成功无提示





## 分区的格式化与挂载

> **Warning**
>
> 警告：除非你清楚你自己在做什么，否则请不要对硬盘分区表、以及除自己新建的分区之外的分区进行任何操作，并且请多次检查自己有没有输错命令，以防对其他分区的数据产生影响。
>
> 换而言之，只要你不对其他分区（Windows 相关分区）进行任何操作，就不需要担心有任何数据丢失的风险。



### 新建 EFI 系统分区

如果你在一块 **新硬盘** 上安装 Arch Linux，则需要为其新建一个 EFI 系统分区

> **Note**
>
> **如果你要在一块已经安装有 Windows 的硬盘上安装 Arch Linux，跳过这一步**



列出当前所有设备与分区

```
fdisk -l
```

选择进入即将安装 Arch Linux 的硬盘，形如 `/dev/nvme0n1`（请以自己的设备情况为准）

```
fdisk /dev/nvme0n1
```

使用 `F` 列出当前的未分配空间（即在 Windows 下分配出的空间）

使用 `n` 在未分配空间新建分区，分区号与起始扇区默认即可，终止扇区输入 `+500M`，即给该新分区分配 500M 空间

新建完成后使用 `t`，选中该分区后将该分区标记为 `EFI System` 分区，使用 `L` 可以找到 EFI System 分区类型标号，如无意外应该是 `1`

`w` 保存并退出

格式化该分区，假设该分区名为 `/dev/nvme0n1p1`

```
mkfs.fat -F32 /dev/nvme0n1p1
```



### 新建数据分区

列出当前所有设备与分区

```
fdisk -l
```

选择进入即将安装 Arch Linux 的硬盘，形如 `/dev/nvme0n1`（请以自己的设备情况为准）

```
fdisk /dev/nvme0n1
```

使用 `F` 列出当前的未分配空间（即在 Windows 下分配出的空间）

使用 `n` 在未分配空间新建分区，如无意外全部默认即可

`w` 保存并退出



### 格式化数据分区

列出当前所有分区

```
fdisk -l
```

找到 **EFI System 分区** 以及刚刚建立的 **Linux Filesystem 新分区** ，**记下这两个分区的路径**（形如 `/dev/nvme0n1p1` 与 `/dev/nvme0n1p5`）。EFI 系统分区一般大小为 300-500M 不等。

> 再次提示：单次或多次按下 `Tab` 可以补全或选择可能的选项，免去输入校对之苦。
> 部分电脑蜂鸣器会在 `Tab` 无法补全时发出刺耳的提示声，使用 `rmmod pcspkr` 移除。

格式化刚刚新建的 **数据分区（替换为你自己的路径）**

```shell
mkfs.ext4 /dev/nvme0n1p5
```



### 挂载分区

请将 `nvme0n1p5` 替换为之前创建的数据分区，将 `nvme0n1p1` 替换为已经存在的 EFI 系统分区

```
mount /dev/nvme0n1p5 /mnt
```

```
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot 
```

检查挂载是否成功

```
mount
```



## 配置包管理器与安装基本包

Arch Linux 的包管理器 `pacman` 十分强大，大部分情况下，一行命令就可以搞定软件包及其依赖。

运行命令以配置 `pacman` 所使用的镜像源，`Reflector` 会自动帮我们配置位于 China 的下载速度最快的镜像源

```
reflector --country China --sort rate --latest 5 --save /etc/pacman.d/mirrorlist
```

可能会报 `WARNING` 但无需理会



### 学习 Vim 的使用

休息一下，接下来我们的所有文字编辑命令都需要使用 `Vim`。推荐使用以下的链接进行学习，只需要掌握第一等级即可：

> [简明 VIM 练级攻略](https://coolshell.cn/articles/5426.html)





打开 `pacman` 设置，启用 `pacman` 的并行下载功能，加速下载，事半功倍。

```
vim /etc/pacman.conf
```

找到 `ParallelDownloads = 5` 这一行并取消其注释。



### 安装基本包

执行以下命令，安装 Arch Linux 所需要的基本包

```
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim reflector
```

遇到需要选择的场合一路回车选择默认项即可。



## 生成 Fstab 文件

生成（Generate）自动挂载分区的 `fstab` 文件（即文件系统表 File System Table）

```
genfstab -L /mnt >> /mnt/etc/fstab
```

由于这步比较重要，所以我们需要输出生成的文件来检查是否正确，执行以下命令：

```
cat /mnt/etc/fstab
```

如果前面的挂载操作没有出错，应该输出且 **仅输出** 两条记录：（以你的磁盘分区情况为准）

- 根分区 `/` 被挂载到了此前建立的 **数据分区** `/dev/nvme0n1p5`，分区的文件系统为 `ext4`


- 引导分区 `/boot` 被挂载到了 **硬盘已有的 EFI 系统分区** `/dev/nvme0n1p1`，分区的文件系统为 `vfat`


如果 `fstab` 文件有任何错误，请先删除该文件

```
rm /mnt/etc/fstab
```

检查前面的挂载操作有没有出错，`umount` 之后再重新挂载、生成。





## 新系统的必要配置

> 这里的配置流程虽然有些繁琐，但不会像前面的操作一样容易出错。
>

### Chroot

`Chroot` 意为 `Change root` ，相当于把操纵权交给我们新安装（或已经存在）的 `Linux` 系统，**执行了这步以后，我们的操作都相当于在磁盘上新装的系统中进行**。

执行如下命令：

```
arch-chroot /mnt
```

顺带一提，如果以后系统出现了问题，只要插入任意一个安装有 Arch Linux 镜像的 U 盘并启动，将我们的系统根分区挂载到 `/mnt` 下、EFI 系统分区挂载到 `/mnt/boot` 下，再通过这条命令就可以进入我们的系统进行修复操作。~~（用 Arch 的人身边都应该常备一个急救 U 盘）~~



### 安装必要软件包

打开 `pacman` 设置，启用 `pacman` 的并行下载功能，加速下载，事半功倍。

```
vim /etc/pacman.conf
```

找到 `ParallelDownloads = 5` 这一行并取消其注释，可以将 `5` 调整为你想要的数值。



运行命令以配置 `pacman` 所使用的镜像源，`Reflector` 会自动帮我们配置位于 China 的下载速度最快的镜像源

```
reflector --country China --sort rate --latest 5 --save /etc/pacman.d/mirrorlist
```

可能会报 `WARNING` 但无需理会

> 你可能会发现，此处对 pacman 进行的操作似曾相识。这是由于之前我们进行配置的对象是 U 盘系统之中的 pacman，在 chroot 到新的系统后需要重新配置



目前，系统根目录已经从 U 盘切换到了硬盘中，需要安装一些必需的软件包

```
pacman -S dialog wpa_supplicant ntfs-3g networkmanager netctl git
```

遇到需要选择的场合一路回车选择默认项即可。



### 设置时区、地区与语言信息

依次执行如下命令设置我们的时区为上海，并生成相关文件

```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```



执行如下命令，设置我们使用的语言选项

```
vim /etc/locale.gen
```

在文件中找到 `en_US.UTF-8 UTF-8`、`zh_CN.UTF-8 UTF-8` 、`zh_HK.UTF-8 UTF-8` 及 `zh_TW.UTF-8 UTF-8`  这四行，去掉行首的 # 号，保存并退出。

执行如下命令，系统会生成我们需要的本地化文件

```
locale-gen
```



打开（不存在时会创建）`/etc/locale.conf`文件：

```
vim /etc/locale.conf
```

在文件的第一行加入以下内容

```
LANG=en_US.UTF-8
```

保存并退出



### 设置主机名

打开（不存在时会创建）`/etc/hostname` 文件：

```
vim /etc/hostname
```

在文件的第一行输入你自己设定的一个 `myhostname`，这将会是你的 **计算机名**，保存并退出。



打开（不存在时会创建）`/etc/hosts` 文件：

```
vim /etc/hosts
```

在文件末添加如下内容（将 `myhostname` 替换成你自己设定的主机名），保存并退出。

```
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname
```



### 设置 Root 密码

`root` 账户是 `Linux` 系统中的最高权限账户，需要设置密码保护起来，以免无意间实施了破坏性的敏感操作。

```
passwd
```



### 新建用户与配置 sudo

> 关于这一步操作的说明，可以查看 [教程](https://www.viseator.com/2017/05/19/arch_setup/#%E6%96%B0%E5%BB%BA%E7%94%A8%E6%88%B7)

请自行替换 `username` 为你想要使用的用户名

```
useradd -m -G wheel username
```

```
passwd username
```

为了在普通用户下使用 root 操作，需要配置 sudo

```
pacman -S sudo
```

```
vim /etc/sudoers
```

找到 `# %wheel ALL=(ALL:ALL) ALL`，取消注释并保存退出。

> 或许有的人已经发现 Arch Wiki 和一些其他教程资料中的命令是以 `#` 或 `$` 开头的，这两个符号就对应着命令行中的命令提示符，`#` 代表以 `Root` 用户执行命令，`$` 代表以普通用户执行命令，平时使用教程中的命令时应该注意这一点。
>



### 安装处理器微码

显然你应该根据你电脑的 CPU 型号选取一个包进行安装

```
pacman -S intel-ucode
pacman -S amd-ucode
```





## 配置系统引导

此处使用 `grub` 进行系统引导，先安装必要的包

```
pacman -S os-prober ntfs-3g grub efibootmgr
```

启用 `os-prober`：编辑 grub 设置，取消注释下述设置项

```
vim /etc/default/grub
```

```
# GRUB_DISABLE_OS_PROBER=false
```

部署 `grub`

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
```

生成配置文件

```
grub-mkconfig -o /boot/grub/grub.cfg
```

检查文件末尾的 `menuenrtry` 是否有 Arch Linux 入口

```
vim /boot/grub/grub.cfg
```



若有任何报错请查阅 Arch Wiki、教程或自行搜索





## 创建交换文件

交换文件可以在物理内存不足的时候将部分内存暂存到交换文件中，避免系统由于内存不足而完全停止工作。之前通常采用单独一个分区的方式作为交换分区，现在更推荐采用交换文件的方式，更便于我们的管理。分配一块空间用于交换文件，执行：

```
dd if=/dev/zero of=/swapfile bs=1M count=8192 status=progress
```

将 `8192` 换成需要的大小，单位 Mb，一般与计算机 RAM 大小一致即可。

更改权限，执行：

```
chmod 600 /swapfile
```

设置交换文件，执行：

```
mkswap /swapfile
```

启用交换文件，执行：

```
swapon /swapfile
```

最后我们需要编辑 `/etc/fstab` 为交换文件设置一个入口，使用 `Vim` 打开文件：

```
vim /etc/fstab
```

**注意编辑 `fstab` 文件的时候要格外注意不要修改之前的内容，直接在最后新起一行加入以下内容**：

```
/swapfile none swap defaults 0 0
```





## 安装图形界面

> 再次提醒，你应当开启 `pacman` 的并行下载功能
>
> 遇到需要选择的场合一路回车选择默认项即可
>

安装 Xorg 图形服务

```
pacman -S xorg
```

初次安装一般在 KDE 与 Gnome 之间选择

### KDE Plasma

安装 KDE Plasma

```
pacman -S plasma kde-applications
```

安装桌面管理器 sddm

```
pacman -S sddm
```

设置 sddm 开机启动

```
systemctl enable sddm
```



### GNOME

安装 GNOME

```
pacman -S gnome gdm
```

设置 gdm 开机启动

```
systemctl enable gdm
```



### 网络服务

启用适用于桌面环境的网络服务 `NetworkManager`

```
systemctl disable netctl
systemctl enable NetworkManager
```

`reboot`，进入图形界面



## 安装后配置与提示

这里的内容根据本人习惯配置进行，时不时更新一下，方便存档



### 社区支持

Arch Linux 有完善的 Wiki 与活跃的社区支持，有任何问题先在 Wiki 内查找以及 Google 一下， 绝大部分问题都会找到解决方案



### 滚动更新

在使用之前，要了解 Arch Linux 使用的是激进的滚动更新策略，你的系统可以时刻保持并且也 **必须保持** 最新的内核版本与最新的软件版本。

因此在你安装软件包的时候，`pacman` 的命令应当从简单的 *安装* 变为 **安装软件包+同步软件资料库+进行全面系统更新**，也就是说：

```
sudo pacman -S package-name
```

应当改为

```
sudo pacman -Syu package-name
```



### 代理配置

如果你使用 clash 进行国际联网，可以直接下载方便使用的 [Clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg)，解压后打开 `cfw` 即可

若提示依赖缺失，可以安装 gtk3，[参考](https://aur.archlinux.org/packages/clash-for-windows-bin)

```
sudo pacman -Syu gtk3
```



- 在 `System Settings` 中搜索 `proxy`，手动将代理地址设置为 `127.0.0.1`，cfw 默认端口为 `7890`

- 设置 `Git` 代理：

  ```
  git config --global http.proxy "http://127.0.0.1:7890"
  ```

- 设置终端代理：将 cfw 中的命令手动粘贴到终端中回车，但只在本次对话中生效

  ```
  export https_proxy=http://127.0.0.1:7890;
  export http_proxy=http://127.0.0.1:7890;
  export all_proxy=socks5://127.0.0.1:7890
  ```



### AUR helper: yay

Arch Linux 除了官方源之外，还拥有广大社区用户维护的 **Arch 用户软件仓库**（Arch User Repository，简称 AUR）可供使用，极大丰富了 Arch Linux 的软件库，用户体验++

安装可以让我们便捷安装 AUR 包的 `yay`

```
pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```



### Code - OSS or VS Code

前者开源，后者是微软基于开源项目二次开发的软件，可以类比 Chromium 与 Google Chrome

安装 Code - OSS

```
sudo pacman -Syu code
```

或者 Visual Studio Code

```
yay -Syu visual-studio-code-bin
```

装好之后可以用 code 代替 nano 或 vim 等命令行编辑器进行文本编辑

可以修改 git 的默认编辑器为 code

```
git config --global core.editor "code --wait"
```



### zsh 与 oh-my-zsh

zsh 比系统默认搭载的 bash 更好用，搭配上社区支持的项目 [oh-my-zsh](https://ohmyz.sh/)，插件和主题都很齐全

```
yay -Syu zsh
```

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

- 切换默认 shell 为 zsh

```
sudo chsh -s /bin/zsh username
```

reboot 后再次打开终端，或直接运行 zsh，就能看到带颜色的默认 robbyrussell 主题 zsh 了

- 编辑 `~/.zshrc`，找到 plugins 行，加入我推荐的几个插件

```
code ~/.zshrc
```

```
plugins=(git z sudo zsh-syntax-highlighting zsh-autosuggestions)
```

其中 [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh) 与 [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh) 需要自行安装，或者可以看这个 [gist](https://gist.github.com/dogrocker/1efb8fd9427779c827058f873b94df95)

接着在 `.zshrc` 中粘贴上文代理中的三行终端命令，zsh 每次启动都会读取 `.zshrc` 初始化，即可自动化配置终端代理



### Git

随手配配能用就行

- 代理和配置编辑器在前面提到了

- [git-credential-manager](https://github.com/GitCredentialManager/git-credential-manager)：AUR 下一个，存 GitHub HTTPS 密钥用

- 给 commit 整一下 [GPG 签名](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)



### 中文字体与中文输入法

在系统语言设置内加入中文，安装中文字体与中文输入法后重启即可

```
yay -Syu noto-fonts-cjk noto-fonts-emoji
```

可以使用 fcitx4 的搜狗输入法，或在 fcitx5 的拼音输入法中导入搜狗词库，参照 [fcitx](https://wiki.archlinux.org/title/fcitx#Chinese) 与 [fcitx5](https://wiki.archlinux.org/title/fcitx5#Chinese)

安装 fcitx5 及组件，在设置中添加输入法即可，具体参照 Arch Wiki
```
yay -Syu fcitx5-im fcitx5-chinese-addons fcitx5-qt fcitx5-gtk
```

修改 `/etc/environment` 文件，在文件开头加入如下内容：

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
INPUT_METHOD=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibus
```

可以解决一些软件无法调出 `fcitx` 的问题



### 蓝牙配置

参照 [Arch wiki](https://wiki.archlinux.org/title/Bluetooth#Installation)

```
yay -Syu bluez bluez-utils blueman
```

```
systemctl start bluetooth.service && systemctl enable bluetooth.service
```



### 浏览器

没什么好说的，~~但我喜欢用 Edge~~

```
yay -Syu google-chrome
```

```
yay -Syu microsoft-edge-dev-bin
```

```
yay -Syu chromium
```

 

### QQ 和微信

使用 yay 安装 AUR 包即可

linuxqq

[deepin-wine-wechat](https://github.com/vufa/deepin-wine-wechat-arch)



### Grub 引导炸了

~~小心 Windows~~

插入写入有 Arch Linux 安装介质的 U 盘，联网后先 mount `/` 与 `/boot`（参考 **挂载分区** 一节）， 之后 `arch-chroot` 到 `/mnt` 进行抢救，先重装一下内核（`pacman -S linux`）这一步会自动为你重新生成 `initramfs` 镜像，然后跳至 **配置系统引导** 的 **部署 grub** 环节照做。