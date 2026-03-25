---
title: 黑群晖安装教程
published: 2024-11-08
updated: 2026-02-11
description: '黑群晖安装教程'
image: ''
tags: [docker,nas]
category: 'nas'
draft: false
---

#####	Pve安装群晖
- 将下 img 文件上传的 pve
- 上传完成后在控制台得到文件路径
``` bash
target file: /var/lib/vz/template/iso/DS918_7.21-69057______.img
```
- pve 宿主机执行：
``` bash
qm importdisk <VM ID> <上传文件的绝对路径> local-lvm
# qm importdisk 103 /var/lib/vz/template/iso/DS918_7.21-69057______.img local-lvm
```
- 回到创建的虚拟机页面
- 选择 `未使用的磁盘` - 编辑
    - 总线 / 设备：SATA
    - 添加
- 选项 - 引导顺序，**勾选**新添加的磁盘并调整为**第一位**
![Pasted image 20240603142911](https://image.czys.xn--6qq986b3xl/rest/2024/06/7xMGHfK.png)
- 启动使用**Synology Assistant**查找IP进行登录
![](https://image.czys.xn--6qq986b3xl/rest/2024/06/dqL6HfK.png)
- 安装固件

###### [PVE - 群晖](https://blog.nipx.cn/pages/a833bb/#%E5%87%86%E5%A4%87)
###### [黑群晖NAS保姆级教程](https://post.smzdm.com/p/ar0v2on7/)