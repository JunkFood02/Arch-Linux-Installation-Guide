# Arch Linux 安装简明流程

这是一篇为 GPT/EFI 引导的电脑安装 Arch Linux（双系统）的中文简明流程，尽可能省略了可以省略的流程与文字以使得篇幅尽量短小，基本上基于 [Arch Wiki](https://wiki.archlinux.org/title/installation_guide) 以及这篇 [优秀的安装教程](https://www.viseator.com/2017/05/17/arch_install/)，特此表示感谢。

本文严格按照操作顺序进行编写，还请认真阅读每一步操作的说明以及注意事项。

## 安装前准备

1. 大于 60G 的硬盘空余空间。
2. 一块储存空间至少 4G 的 U 盘。
3. 稳定，而不需要额外进行设备认证的有线/无线网络连接，如手机热点等。
4. 至少 3 小时~~或长达数星期~~的时间。
5. 基本的搜索引擎使用能力以及英语阅读能力。



## 准备安装介质与磁盘分区

### 磁盘安装介质

此处默认你使用 Windows 系统，其他情况请参阅 [Arch Wiki](https://wiki.archlinux.org/title/USB_flash_installation_medium)。

1. 下载 Arch Linux 最新的系统镜像，在下载页面下方找到 China 的镜像源，如`tuna.tsinghua.edu.cn`，下载文件名形如 `archlinux-2022.04.05-x86_64.iso` 的 iso 镜像文件。
2. 下载 [Rufus](https://rufus.ie/zh/)，解压即用的便携版即可。
3. 使用 Rufus 将 Arch Linux 镜像装载到 U 盘，所有参数保持默认不修改即可，如果 U 盘内存放有数据请先备份。

### 磁盘分区

此处默认你使用 Windows 系统，其他情况请自行搜索。

1. 使用 Windows 自带的磁盘管理，你可以直接在开始菜单中搜索找到它，或者右键单击计算机，选择管理。
2. **压缩** 有空余空间的磁盘分区，分配一块空间给 Arch Linux，越多越好，最小不要小于 60G。
3. 在压缩后的空闲空间新建一个简单卷（分区），卷标分配与格式化与否无所谓。



## BIOS 设置

不同品牌的电脑/主板进入 BIOS 