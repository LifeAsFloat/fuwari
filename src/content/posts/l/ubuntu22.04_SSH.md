---
title: 【Ubuntu】Ubuntu22.04安装SSH服务
published: 2024-11-08
updated: 2026-02-11
description: '【Ubuntu】Ubuntu22.04安装SSH服务'
image: ''
tags: [ubuntu,ssh,linux]
category: 'linux'
draft: false
---

#### ubuntu安装ssh

- 更新 Linux 系统的包索引或包列表
``` bash
sudo apt update
```
-  安装ssh服务端
``` bash
sudo apt install openssh-server
```
- 配置文件
``` bash
sudo vim /etc/ssh/ssh_config
```
- ssh相关服务
``` bash
# 重新启动ssh服务
deng@local:~/桌面$ sudo systemctl restart ssh

# 启动ssh服务
deng@local:~/桌面$ sudo systemctl start ssh

# 查看ssh服务状态
deng@local:~/桌面$ sudo systemctl status ssh 

# 允许开机启动ssh服务
sudo systemctl enable ssh
```
- ufw相关服务(默认即可)
``` bash
sudo ufw status 
sudo ufw enable 
# 确保防火墙允许服务端口
sudo ufw allow ssh
sudo ufw status
```


[【Ubuntu】Ubuntu22.04安装SSH服务](https://blog.csdn.net/dengjin20104042056/article/details/131745236)