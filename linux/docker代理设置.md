---
title: My First Blog Post
published: 2023-09-09
description: This is the first post of my new Astro blog.
image: ./cover.jpg
tags:
  - linux
  - docker
category: Docker
draft: false
---
### Docker 版本在 23.0以上

1. **创建或修改 Docker 守护进程配置文件**
在 Linux 系统中，创建或编辑 `/etc/docker/daemon.json` 文件：
```bash
sudo mkdir -p /etc/docker
sudo vi /etc/docker/daemon.json
```
2. **添加代理配置**
在 daemon.json 中添加以下内容（根据您的实际代理地址修改）：
```json
{
  "proxies": {
    "http-proxy": "http://proxy.example.com:port",
    "https-proxy": "http://proxy.example.com:port",
    "no-proxy": "localhost,127.0.0.1"
  }
}
```
### Docker 版本在 23.0以下

1. **为 Docker 服务创建代理配置目录**
```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
```
2. **创建代理配置文件**
创建 `/etc/systemd/system/docker.service.d/http-proxy.conf` 文件：
```bash
sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf
```
添加以下内容：
```ini
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:port"
Environment="HTTPS_PROXY=http://proxy.example.com:port"
Environment="NO_PROXY=localhost,127.0.0.1"
```
5. **重启 Docker 服务**
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```
6. **验证配置**
可以通过以下命令验证代理设置是否生效：
```bash
sudo systemctl show --property=Environment docker
```
**注意事项：**
- 请将 `proxy.example.com:port` 替换为您实际的代理服务器地址和端口
- 如果代理服务器需要认证，格式为：`http://username:password@proxy.example.com:port`
- `NO_PROXY` 变量用于指定不需要代理的地址
- 如果您使用的是 Docker Desktop（Windows/Mac），可以通过图形界面设置代理：
  - 打开 Docker Desktop
  - 点击设置（Settings）
  - 在 Resources > PROXIES 部分配置代理