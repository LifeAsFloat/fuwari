---
title: Watchtower使用教程
published: 2024-11-08
updated: 2026-02-11
description: 'Watchtower使用教程'
image: ''
tags: [docker]
category: 'docker'
draft: false
---

### 1. 安装 Docker

确保已经在 Windows 上安装了 Docker。如果还没有安装，可以访问[Docker官网](https://www.docker.com/products/docker-desktop/) 下载并安装 Docker Desktop for Windows。
### 2. 启动 Docker

安装完成后，启动 Docker Desktop 并确保 Docker 正常运行。可以通过在命令行中运行以下命令来验证 Docker 是否正常运行：
```shell
docker --version
```
### 3. 拉取 Watchtower 镜像

使用 Docker 命令拉取 Watchtower 镜像：
```shell
docker pull containrrr/watchtower
```
### 4. 启动 Watchtower 容器

启动 Watchtower 容器，并指定希望它监控和更新的容器。可以使用以下命令：
```shell
docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower
```
这条命令将启动 Watchtower 并让它监控所有正在运行的容器。如果只想监控特定的容器，可以在命令后面加上容器的名字或 ID。例如：
```shell
docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower my-container
```
### 5. 配置 Watchtower 选项

可以通过传递环境变量或命令行参数来配置 Watchtower。例如，如果希望 Watchtower 每 5 分钟检查一次更新，可以使用以下命令：
```shell
docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower \
  --interval 300
```
其他常用的选项包括：
- `--cleanup`: 删除旧的容器
- `--no-startup-message`: 禁止启动时的日志输出
- `--run-once`: 只运行一次更新检查
### 6. 验证 Watchtower 是否正常运行

可以通过查看 Watchtower 容器的日志来验证它是否正常运行。使用以下命令查看日志：
```shell
docker logs watchtower
```
### 7. 停止和移除 Watchtower 容器

如果需要停止 Watchtower，可以使用以下命令：
```shell
docker stop watchtower
```
如果需要移除 Watchtower 容器，可以使用以下命令：
```shell
docker rm watchtower
```