---
title: "虚拟机麒麟系统安装 VMware Tools"
date: 2022-07-15
draft: false
isCJKLanguage: true
tags: ["VMware", "麒麟系统"]
---

系统信息
- 主机：Windows 10 Pro
- 虚拟机： VMware Workstation 16 Pro 16.2.3
- 麒麟系统：Kylin-Server-10-SP1-Release-Build20-20210518-x86_64

虚拟机的窗口分辨率无法自适应麒麟系统的分辨率，其他系统可以通过自动`安装 VMware Tools`解决，不过这无法用于麒麟系统，因为"`安装 VMware Tools`"是灰色的点不了。

以下是手动安装 VMware Tools 步骤：

1. 麒麟虚拟机关机
2. 打开麒麟虚拟机设置
   > VMware 选中麒麟虚拟机 -> 右键点击 -> 选择“设置”
3. 挂载 VMware linux
   > 上一步打开的”虚拟机设置“弹窗中点击”添加(A)...“
   > 
   > 选择”CD/DVD 驱动器“，点击”完成“
   > 
   > 在”虚拟机设置“弹窗中找到刚添加的”CD/DVD“，选中它，在它右边会显示属性”设备状态“、”连接“
   > 
   > ”设备状态“下勾选`启动时连接(O)`
   > 
   > ”连接“下选择`使用 ISO 映像文件(M):`，点击它下面的`游览(B)...`按钮，在弹窗中找到 VMware 安装目录下的 linux.iso
   > 
   > 点击”确定“，保存虚拟机设置
4. 麒麟虚拟机开机，进入虚拟机打开`我的电脑`可以看到`可移动存储设备`下有两个盘
   - VMware Tools
   - Kylin-Server-10
5. 进入`VMware Tools` 目录，在目录内空白处点击右键，选择`在终端中打开(T)`
6. 解压 VMwareTools
   ```shell
   tar zxvf VMware-10.3.23-16594550.tar.gz -C /opt
   ```
7. 安装 VMwareTools
   ```shell
   /opt/vmware-tools-distrib/vmware-install.pl
   ```
   执行命令后有选项的话全部按`Enter`键
