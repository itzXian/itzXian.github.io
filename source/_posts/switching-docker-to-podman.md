---
title: 使用Podman替换Docker
date: 2025-11-20 00:11:42
tags:
  - docker
  - podman
---

Docker默认需要`root`权限，虽然也提供有Rootless模式，但是懒得去配置了，直接卸载换Podman。

Podman配置好镜像直接开用，命令基本跟Docker一样。

镜像地址来自https://github.com/dongyubin/DockerHub

把下面内容加到全局配置`/etc/containers/registries.conf`或者用户配置`~/.config/containers/registries.conf`里边。

顺带一提现在`unqualified-search-registries`并不被Podman推荐使用。

```
unqualified-search-registries = ["docker.io"]

[[registry]]
prefix = "docker.io"
location = "docker.1panel.live"
insecure = true

[[registry.mirror]]
location = "docker.1ms.run"
insecure = true
[[registry.mirror]]
location = "dytt.online"
insecure = true
[[registry.mirror]]
location = "lispy.org"
insecure = true
[[registry.mirror]]
location = "docker-0.unsee.tech"
insecure = true
[[registry.mirror]]
location = "docker.xiaogenban1993.com"
insecure = true
[[registry.mirror]]
location = "666860.xyz"
insecure = true
[[registry.mirror]]
location = "hub.rat.dev"
insecure = true
[[registry.mirror]]
location = "docker.m.daocloud.io"
insecure = true
[[registry.mirror]]
location = "demo.52013120.xyz"
insecure = true
[[registry.mirror]]
location = "proxy.vvvv.ee"
insecure = true
[[registry.mirror]]
location = "registry.cyou"
insecure = true
```
