---
title: 获取文件hash值
published: 2024-11-08
updated: 2026-02-11
description: '获取文件hash值'
image: ''
tags: [powershell,windows]
category: 'powershell'
draft: false
---

windows系统:
```powershell
certutil -hashfile "D:\Download\PicList-Setup-2.9.0-ia32.exe" Sha512
```
-  sha256 计算hash256值
- MD5 计算md5码
![image.png](https://image.czys.xn--6qq986b3xl/rest/2024/06/HnNmGfK.png)

