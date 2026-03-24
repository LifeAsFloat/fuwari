---
title: scoop安装教程
published: 2024-11-08
updated: 2026-02-11
description: 'scoop安装教程'
image: ''
tags: [powershell,windows]
category: 'powershell'
draft: false
---

```powershell
# 设置环境
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
irm get.scoop.sh -outfile 'install.ps1'
.\install.ps1  -RunAsAdmin -ScoopDir 'D:\scoop\user' -ScoopGlobalDir 'D:\scoop\global' -NoProxy
# 添加镜像
scoop bucket add main https://mirror.nju.edu.cn/git/scoop-extras.git/ 
scoop bucket add extras https://mirror.nju.edu.cn/git/scoop-extras.git/ 
# 更新scoop
scoop update
```
