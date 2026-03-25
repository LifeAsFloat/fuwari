---
title: Windows子系统(WSL2)中自定义安装Ubuntu并换源
published: 2024-11-08
updated: 2026-02-11
description: 'Windows子系统(WSL2)中自定义安装Ubuntu并换源'
image: ''
tags: [wsl,ubuntu,linux]
category: 'linux'
draft: false
---

## 先决条件

Windows 10 版本 2004 及更高版本（内部版本 19041 及更高版本）或 Windows 11。

## 安装

以管理员身份打开 PowerShell 或 Windows 命令提示符中输入命令 `wsl --install`，然后重启计算机来安装运行适用于 Linux 的 Windows 子系统 (WSL) 所需的全部内容。

\--install 命令执行以下操作：

-   启用可选的 WSL 和虚拟机平台组件
-   下载并安装最新 Linux 内核
-   将 WSL 2 设置为默认值
-   下载并安装 Ubuntu Linux 发行版（可能需要重新启动）

默认情况下，安装的 Linux 分发版为 Ubuntu。 可以使用 `-d` 标志进行更改。

-   若要更改安装的发行版，请输入：`wsl --install -d <Distribution Name>`。 将 `<Distribution Name>` 替换为要安装的发行版的名称。
-   若要查看可通过在线商店下载的可用 Linux 发行版列表，请输入：`wsl --list --online` 或 `wsl -l -o`。
-   若要在初始安装后安装其他 Linux 发行版，还可使用命令：`wsl --install -d <Distribution Name>`。

例如此次安装使用的命令为： `wsl --install -d Ubuntu-20.04`

## 设置 Linux 用户名和密码

使用 WSL 安装 Linux 发行版的过程完成后，使用“开始”菜单打开该发行版（默认情况下为 Ubuntu）。系统将要求你为 Linux 发行版创建“用户名”和“密码”。

-   此**用户名**和**密码**特定于安装的每个单独的 Linux 分发版，与 Windows 用户名无关。
-   创建**用户名**和**密码**后，该帐户将是分发版的默认用户，并将在启动时自动登录。
-   此帐户将被视为 Linux 管理员，能够运行 `sudo` (Super User Do) 管理命令。
-   在 WSL 上运行的每个 Linux 发行版都有其自己的 Linux 用户帐户和密码。 每当添加分发版、重新安装或重置时，都必须配置一个 Linux 用户帐户。

若要更改或重置密码，请打开 Linux 发行版并输入命令：`passwd`。 系统会要求你输入当前密码，然后要求输入新密码，之后再确认新密码。

如果忘记了 Linux 分发版的密码：

1.  请打开 PowerShell，并使用以下命令进入默认 WSL 分发版的根目录：`wsl -u root`
    
    > 如果需要在非默认分发版中更新忘记的密码，请使用命令：`wsl -d Debian -u root`，并将 `Debian` 替换为目标分发版的名称。
    
2.  在 PowerShell 内的根级别打开 WSL 发行版后，可使用此命令更新密码：`passwd <username>`，其中 `<username>` 是发行版中帐户的用户名，而你忘记了它的密码。
3.  系统将提示你输入新的 UNIX 密码，然后确认该密码。 在被告知密码已成功更新后，请使用以下命令在 PowerShell 内关闭 WSL：`exit`。

使用北京外国语大学开源镜像站，地址：[ubuntu | 镜像站使用帮助 | 北京外国语大学开源软件镜像站 | BFSU Open Source Mirror](https://mirrors.bfsu.edu.cn/help/ubuntu/)。

Ubuntu 的软件源配置文件是 `/etc/apt/sources.list`。将系统自带的该文件做个备份，将该文件替换为下面内容，即可使用 TUNA 的软件源镜像。

```sh
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释 deb https://mirrors.bfsu.edu.cn/ubuntu/ focal main restricted universe multiverse # deb-src https://mirrors.bfsu.edu.cn/ubuntu/ focal main restricted universe multiverse deb https://mirrors.bfsu.edu.cn/ubuntu/ focal-updates main restricted universe multiverse # deb-src https://mirrors.bfsu.edu.cn/ubuntu/ focal-updates main restricted universe multiverse deb https://mirrors.bfsu.edu.cn/ubuntu/ focal-backports main restricted universe multiverse # deb-src https://mirrors.bfsu.edu.cn/ubuntu/ focal-backports main restricted universe multiverse deb https://mirrors.bfsu.edu.cn/ubuntu/ focal-security main restricted universe multiverse # deb-src https://mirrors.bfsu.edu.cn/ubuntu/ focal-security main restricted universe multiverse # 预发布软件源，不建议启用 # deb https://mirrors.bfsu.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse # deb-src https://mirrors.bfsu.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

## 修改安装目录

默认安装目录在c盘，比较占用系统盘空间，因此此处将它移动到d盘。

首先查看所有分发版本

```powershell
wsl -l -v
```

导出分发版为tar文件到d盘

```powershell
wsl --export Ubuntu-20.04 d:\ubuntu20.04.tar
```

注销当前分发版

```powershell
wsl --unregister Ubuntu-20.04
```

重新导入并安装分发版在d:\\ubuntu

```powershell
wsl --import Ubuntu-20.04 d:\ubuntu d:\ubuntu20.04.tar --version 2
```

设置默认登陆用户为安装时用户名（不要更改成别的名字）

```powershell
ubuntu2004 config --default-user <你之前的用户名>
```

## 参考

1.  [安装 WSL](https://docs.microsoft.com/zh-cn/windows/wsl/install)
