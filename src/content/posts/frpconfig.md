---
title: frp配置
published: 2026-02-04
updated: 2026-02-27
description: 'frp配置'
image: ''
tags: [docker,frp]
category: 'frp'
draft: false 
---

**frp TLS 全场景配置万能文档**。包含了证书生成、不同场景（IP/域名）以及不同验证模式（单向/双向）的完整配置。

---

### 第一步：准备证书（统一使用 OpenSSL）

为了支持 **IP 地址** 校验，必须使用配置文件生成带 **SAN (Subject Alternative Name)** 的证书。

#### 1. 创建配置文件 `openssl.cnf`
请根据实际情况修改 `IP.1` 或 `DNS.1`。
```ini
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req
prompt = no

[req_distinguished_name]
CN = example.com  # 随意填写，主要看下面的 SAN

[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth, clientAuth
subjectAltName = @alt_names

[alt_names]
IP.1 = 1.2.3.4          # 替换为你的服务器公网 IP
DNS.1 = yourdomain.com  # 替换为你的域名（如果没有则删掉此行）
```

#### 2. 一键生成所有证书
```bash
# 1. 生成 CA 根证书
openssl genrsa -out ca.key 2048
openssl req -x509 -new -nodes -key ca.key -subj "/CN=MyfrpCA" -days 3650 -out ca.crt

# 2. 生成服务端证书 (关联 openssl.cnf)
openssl genrsa -out server.key 2048
openssl req -new -key server.key -config openssl.cnf -out server.csr
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 3650 -extensions v3_req -extfile openssl.cnf

# 3. 生成客户端证书 (双向验证才需要)
openssl genrsa -out client.key 2048
openssl req -new -key client.key -subj "/CN=frp-client" -out client.csr
openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -days 3650
```

---

### 第二部分：场景化配置

#### 场景 A：使用 IP 地址 + 双向验证 (最高安全级别)
适合追求极致安全，且没有域名的用户。

**服务端 `frps.toml`**
```toml
bindPort = 7000
auth.token = "your_token"

[transport.tls]
force = true
certFile = "/etc/frp/ssl/server.crt"
keyFile = "/etc/frp/ssl/server.key"
trustedCaFile = "/etc/frp/ssl/ca.crt" # 验证客户端证书
```

**客户端 `frpc.toml`**
```toml
serverAddr = "1.2.3.4"
serverPort = 7000
auth.token = "your_token"

[transport.tls]
enable = true
certFile = "/etc/frp/ssl/client.crt"     # 客户端证书
keyFile = "/etc/frp/ssl/client.key"      # 客户端私钥
trustedCaFile = "/etc/frp/ssl/ca.crt"    # 验证服务端证书
serverName = "1.2.3.4"        # 必须与证书中的 IP.1 一致
```

---

#### 场景 B：使用域名 + 单向验证 (标准 HTTPS 模式)
适合有域名，且希望配置简单、方便多客户端接入的用户。

**服务端 `frps.toml`**
```toml
bindPort = 7000
auth.token = "your_token"

[transport.tls]
force = true
certFile = "/etc/frp/ssl/server.crt"
keyFile = "/etc/frp/ssl/server.key"
# 单向验证：不要写 trustedCaFile
```

**客户端 `frpc.toml`**
```toml
serverAddr = "yourdomain.com"
serverPort = 7000
auth.token = "your_token"

[transport.tls]
enable = true
trustedCaFile = "/etc/frp/ssl/ca.crt"    # 验证服务端域名证书
serverName = "yourdomain.com" # 必须与证书中的域名一致
```

---

### 第三部分：1Panel 部署手册

如果您使用 1Panel 的 Docker 版本部署，请注意路径映射。

1.  **宿主机存放路径**：建议放在 `/opt/1panel/apps/frps/ssl/`。
2.  **Docker 映射配置**：
    *   **宿主机目录**：`/opt/1panel/apps/frps/ssl`
    *   **容器目录**：`/etc/frp/ssl`
3.  **配置文件路径写绝对路径**：
    *   在 `frps.toml` 中，路径必须写 `/etc/frp/ssl/server.crt`。

---

### 第四部分：配置要点总结（避坑指南）

| 配置项            | 说明                     | 常见错误                                                     |
| :---------------- | :----------------------- | :----------------------------------------------------------- |
| **serverName**    | 握手时的域名/IP 校验     | **千万不能空着**。必须加引号且值要和证书一致。               |
| **trustedCaFile** | 验证对方身份的“红头文件” | **服务端配了它就是双向验证**，客户端必须传证书，否则报 EOF。 |
| **force = true**  | 强制 TLS 模式            | 开启后，如果客户端没开 TLS (`enable=true`)，直接无法连接。   |
| **Boolean 值**    | `true` / `false`         | **严禁加引号**。加了引号会报 `cannot unmarshal string`。     |
| **文件权限**      | Linux 系统权限           | `.key` 文件建议设为 `600`，`.crt` 设为 `644`。               |

### 验证 TLS 是否生效？
1.  **看日志**：如果 `frps` 开启了 `force = true` 且 `frpc` 成功连上并显示 `start proxy success`，说明 TLS 已经成功握手。
2.  **模拟失败**：尝试把 `frpc.toml` 里的 `serverName` 改错。如果报错 `x509: certificate is valid for xxx, not yyy`，说明 TLS 校验机制工作完美。