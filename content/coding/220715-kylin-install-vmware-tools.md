---
title: "虚拟机麒麟系统安装 VMware Tools"
date: 2022-07-15
draft: false
isCJKLanguage: true
tags: ["VMware", "麒麟系统"]
---

- 主机：Windows 10 Pro
- 虚拟机： VMware Workstation 16 Pro 16.2.3
- 麒麟系统：Kylin-Server-10-SP1-Release-Build20-20210518-x86_64

虚拟机的窗口与麒麟系统的分辨率无法自适应，其他系统可以通过自动`安装 VMware Tools`解决，不过这无法用于麒麟系统，因为"`安装 VMware Tools`"是灰色的点不了。

以下是手动安装 VMware Tools 步骤：

1. 麒麟虚拟机关机
2. VMware 操作步骤
   - 左键点击选中麒麟虚拟机 -> 右键点击它 -> 选择`设置`
   - 在上一步打开的`虚拟机设置`弹窗中点击`添加(A)...` -> 选择`CD/DVD 驱动器` -> 点击`完成`
   - 回到`虚拟机设置`弹窗，找到刚添加的`CD/DVD`，左键点击选中它
     - `设备状态`：勾选`启动时连接(O)`
     - `连接`：选择`使用 ISO 映像文件(M):`，文件为 VMware 安装目录下的 linux.iso
   - 点击”确定“，保存虚拟机设置
3. 麒麟虚拟机操作步骤
   - 虚拟机开机
   - 打开`我的电脑`可以看到`可移动存储设备`下有两个盘：VMware Tools、Kylin-Server-10
   - 进入`VMware Tools`目录，在目录内空白处点击右键，选择`在终端中打开(T)`
   - 执行命令（执行命令后有选项的话全部按`Enter`键）
     ```shell
     tar zxvf VMware-10.3.23-16594550.tar.gz -C /opt
     /opt/vmware-tools-distrib/vmware-install.pl
     ```
