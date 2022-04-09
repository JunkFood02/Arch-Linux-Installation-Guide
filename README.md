# Arch Linux 安装简明流程

这是一篇为 **GPT/EFI 引导** 的电脑安装 Arch Linux（双系统）的中文简明流程，尽可能省略了可以省略的流程与文字以使得篇幅尽量短小，基本上基于 [Arch Wiki Installation guide](https://wiki.archlinux.org/title/installation_guide) 以及由联创团队 Android 组学长的这篇 [优秀的安装教程](https://www.viseator.com/2017/05/17/arch_install/)，特此表示感谢。

本文严格按照操作顺序进行编写，还请认真阅读每一步操作的说明以及注意事项。

> 说明：下文中所指教程均指此篇教程: [以官方Wiki的方式安装ArchLinux](https://viseator.github.io/2017/05/17/arch_install/)。


[TOC]



## 安装前准备

1. 大于 60G 的硬盘空余空间。
2. 一块储存空间至少 4G 的 U 盘。
3. 稳定，而不需要额外进行设备认证的有线/无线网络连接，如手机热点等。
4. 至少 3 小时~~或长达数星期~~的时间。
5. 基本的搜索引擎使用能力以及英语阅读能力。



## 准备安装介质与磁盘分区

### 安装介质

此处默认你使用 Windows 系统，其他情况请参阅 [Arch Wiki](https://wiki.archlinux.org/title/USB_flash_installation_medium)。

1. 下载 Arch Linux 最新的系统镜像，在 [下载页面](https://archlinux.org/download/) 下方找到 China 的镜像源，如 `tuna.tsinghua.edu.cn`，下载文件名形如 `archlinux-2022.04.05-x86_64.iso` 的 iso 镜像文件。
2. 下载 [Rufus](https://rufus.ie/zh/)，解压即用的便携版即可。
3. 使用 Rufus 将 Arch Linux 镜像装载到 U 盘，所有参数保持默认不修改即可，如果 U 盘内存放有数据请先备份。

### 磁盘分区

> 重要提示：如果你的 Windows 电脑为磁盘加上了 **Bitlocker 锁**，请务必解除后再进行任何磁盘操作，否则会为你带来巨大的不幸。

此处默认 Arch Linux 与原系统安装在同一块硬盘上，如果你需要在一块新硬盘上安装，你还需要确定新硬盘的分区表为 GPT 格式，并新建一个 EFI 分区。具体的其他情况请查阅 Arch Wiki、[教程](https://www.viseator.com/2017/05/17/arch_install/) 或自行搜索。

1. 使用 Windows 自带的磁盘管理，你可以直接在开始菜单中搜索找到它，或者右键单击计算机，选择管理。
2. **压缩** 有空余空间的磁盘分区，分配一块空间给 Arch Linux，越多越好，最小不要小于 60G。
3. 在压缩后的空闲空间新建一个简单卷（分区），卷标分配与格式化与否无所谓。
> 说明：你也可以在进入 Arch Linux 安装程序后进行这一步。



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



### 事前故障排除

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



### 连接有线网络

不同型号的无线网卡的支持情况不同，若有条件推荐优先使用有线网网络进行连接，可以直接使用 USB 线将手机连接电脑使用手机的数据网络。

接入网线或手机后，执行命令进行有线网连接

```
dhcpcd
```

测试网络是否联通


```
ping www.baidu.com
```

> 使用 `Ctrl+C` 中止当前正在执行的命令



### 连接无线网络

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



### 设置系统时间

```
timedatectl set-ntp true
```

操作成功无提示



## 分区的格式化与挂载

> 警告：除非你清楚你自己在做什么，否则请不要对硬盘分区表、以及除前文新建的分区之外的分区进行任何操作，并且请多次检查自己有没有输错命令，以防对其他分区的数据产生影响。
>
> 换而言之，只要你不对其他分区（Windows 相关分区）进行任何操作，就不需要担心有任何数据丢失的风险。



### 格式化数据分区

列出当前所有分区

```
fdisk -l
```

找到 **EFI System 分区**以及刚刚在 Windows 下建立的 **新分区** ，**记下这两个分区的路径**（形如 `/dev/` 与 `/dev/nvme0n1p5`）。EFI 系统分区一般大小为 300-500M 不等。

> 再次提示：单次或多次按下 `Tab` 可以补全或选择可能的选项，免去输入校对之苦。
> 部分电脑蜂鸣器会在 `Tab` 无法补全时发出刺耳的提示声，使用 `rmmod pcspkr` 移除

格式化刚刚在Windows下新建的 **数据分区（替换为你自己的路径）**

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

Arch Linux 的包管理器 `pacman` 十分强大，大部分情况下，一行命令就可以搞定包与依赖的问题。

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
pacstrap /mnt base base-devel linux linux-firmware dhcpcd
```

遇到需要选择的场合一路回车选择默认项即可。



## 生成 Fstab 文件

生成（Generate）自动挂载分区的 `fstab` 文件（即系统文件表 File System Table）

```
genfstab -L /mnt >> /mnt/etc/fstab
```

由于这步比较重要，所以我们需要输出生成的文件来检查是否正确，执行以下命令：

```
cat /mnt/etc/fstab
```

如果前面的挂载操作没有出错，应该输出且 **仅输出** 两条记录：（以你的磁盘分区情况为准）

`/` 被挂载到了此前建立的 **数据分区** `/dev/nvme0n1p5` 
`/boot` 被挂载到了 **硬盘已有的 EFI 系统分区** `/dev/nvme0n1p1` 

如果 `fstab` 文件有任何错误，请先删除该文件。

```
rm -rf /mnt/etc/fstab
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

顺带一提，如果以后系统出现了问题，只要插入任意一个安装有 Arch Linux 镜像的 U 盘并启动，将我们的系统根分区挂载到 `/mnt` 下、EFI 系统分区挂载到 `/mnt/boot` 下，再通过这条命令就可以进入我们的系统进行修复操作。



### 安装必要软件包

打开 `pacman` 设置，启用 `pacman` 的并行下载功能，加速下载，事半功倍。

```
vim /etc/pacman.conf
```

找到 `ParallelDownloads = 5` 这一行并取消其注释，可以将 `5` 调整为你想要的数值。

目前，系统根目录已经从 U 盘切换到了硬盘中，需要安装一些必需的软件包

```
pacman -S vim dialog wpa_supplicant ntfs-3g networkmanager netctl sudo
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

若有任何报错请查阅 Arch Wiki、教程或自行搜索。



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



## 新建用户与配置 sudo

> 关于这一步操作的说明，可以查看 [教程](https://www.viseator.com/2017/05/19/arch_setup/#%E6%96%B0%E5%BB%BA%E7%94%A8%E6%88%B7)

请自行替换 `username` 为你想要使用的用户名

```
useradd -m -G wheel username
```

```
passwd username
```

为了在普通用户下使用 root 操作，需要配置 sudoers

```
vim /etc/sudoers
```

找到 `# %wheel ALL=(ALL)ALL`，取消注释并保存退出。



## 安装图形界面

> 再次提醒，你应当开启 `pacman` 的并行下载功能
>
> 遇到需要选择的场合一路回车选择默认项即可
>

安装 Xorg 图形服务

```
sudo pacman -S xorg
```

安装 KDE Plasma

```
sudo pacman -S plasma kde-applications
```

安装桌面管理器 sddm

```
sudo pacman -S sddm
```

设置 sddm 开机启动

```
sudo systemctl enable sddm
```

启用适用于桌面环境的网络服务 `NetworkManager`

```
sudo systemctl disable netctl
sudo systemctl enable NetworkManager
```



