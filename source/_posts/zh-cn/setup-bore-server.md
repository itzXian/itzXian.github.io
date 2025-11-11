---
title: 部署bore服务，转发本地端口实现内网穿透
date: 2025-11-11 07:24:10
tags:
  - bore
---

话不多说直接按照项目的说明 ~~复制粘贴~~ 开干
项目地址：https://github.com/ekzhang/bore

1, 用有公网IP的设备来通过`docker`运行，关于`docker`不多赘述，直接贴命令行
```bash
$ docker run \
    -it --init --rm --network \
  host ekzhang/bore server \
    --min-port 1024 --max-port 65535 \
    --secret random_string
```
自行修改 `--min-port`跟`--max-port`后面的参数为想要设置的最小端口跟最大端口，，以及`random_string`为想要的密码，密码在连接的时候需要用到

2, 设置防火墙放行上面设置的最小端口至最大端口，顺便一提还需要放行`7835`端口，这是`bore`连接时需要用到的端口
```bash
# ufw allow 1024:65535/tcp
# ufw allow 7835/tcp
```

3, 在想要转发端口的内网设备上运行
```bash
$ docker run \
    -it --init --rm --network \
  host ekzhang/bore local \
    <对应本地端口> --to <对应公网IP> \
    --secret random_string
```
可以加上`-p`在最小端口与最大端口之间指定一个要映射到的端口，也可以不加，`bore`会在最小端口与最大端口之间随机安排一个

也可以用包管理器直接安装`bore`，这样就不用通过`docker`来运行了