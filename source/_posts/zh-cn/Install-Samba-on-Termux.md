---
title: Termux部署Samba服务
date: 2025-11-06 06:14:10
tags: android termux samba smb
---

1, 安装Samba
`$ pkg i samba`

2, 创建用户
需要用到`root`权限，可以装个PRoot来模拟`root`环境
安装PRoot
`$ pkg i proot`
运行PRoot
`$ proot -0`
创建用户（自行修改用户名）
`# smbpasswd -a 用户名`
推出PRoot
`# exit`

3, 复制预设的配置文件模板到对应路径
```bash
$ mkdir $PREFIX/etc/samba
$ cp \
  $PREFIX/share/doc/samba/smb.conf.example \
  $PREFIX/etc/smb.conf
```

4, 修改配置文件
Termux带的这个配置文件模板就挺完善的了，按需稍作修改就行
```
[Internal storage]
   comment = Internal storage
   path = /sdcard/
   vfs objects = aio_pthread
   aio_pthread:aio open = yes
   read only = no
   browseable = yes
   writable = yes
   guest ok = no
```
可以按需另加，例
```
[Pic]
   comment = Pictures
   path = /sdcard/Pictures/
   vfs objects = aio_pthread
   aio_pthread:aio open = yes
   read only = no
   browseable = yes
   writable = yes
   guest ok = no
```

5, 尝试启动Samba服务
`$ smbd -i -d3`

6, 尝试连接

7, 创建Termux Service
每次都手动运行命令太麻烦，这里直接创建服务，参考[Termux-services - Termux Wiki](https://wiki.termux.com/wiki/Termux-services)
```
$ pkg i termux-services
$ mkdir -p $PREFIX/var/service/smbd/log
$ ln -sf \
  $PREFIX/share/termux-services/svlogger \
  $PREFIX/var/service/smbd/log/run
$ cat > $PREFIX/var/service/smbd/run << EOF
#!/data/data/com.termux/files/usr/bin/sh
smbd -F -d3
EOF
$ chmod +x $PREFIX/var/service/smbd/run
$ sv enable smbd
$ sv up smbd
