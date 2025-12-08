---
title: Termux部署Snapcast实现多设备音频同步播放
date: 2025-12-05 00:32:08
tags:
  - termux
  - snapcast
  - snapserver
  - snapclient
---
1, 安装Snapcast服务端跟PulseAudio
```shell
$ pkg i snapserver pulseaudio
```

2, 运行`pulseaudio`并且按照[Snapcast官方文档](https://github.com/snapcast/snapcast/blob/develop/doc/player_setup.md#pulseaudio)配置PulseAudio
```shell
pulseaudio --start
pacmd load-module module-pipe-sink file=$PREFIX/tmp/snapfifo sink_name=Snapcast format=s16le rate=48000
pacmd update-sink-proplist Snapcast device.description=Snapcast
pacmd set-default-sink Snapcast
```

3, 下载`snapweb`
`snapweb`是Snapcast的网页客户端，Termux官方打的包没带，所以需要另外下载
网址：https://github.com/snapcast/snapweb/releases
下载完成后可随意解压到一个文件夹里，然后在Snapcast的默认配置文件`$PREFIX/etc/snapserver.conf`中修改`doc_root`的值为解压后文件夹的路径
顺带一提`doc_root`默认值为`$PREFIX/usr/share/snapserver/snapweb`，所以其实可以直接解压到这个路径，这样不改配置文件就可以直接开用了
但是下面这里选择按照官方的说明解压到`$PREFIX/usr/share/snapweb`，然后修改配置文件
```shell
$ curl -o snapweb.zip https://github.com/snapcast/snapweb/releases/latest/download/snapweb.zip
$ unzip snapweb.zip $PREFIX/usr/share/snapweb/
sed i 's/snapserver\/snapweb/snapweb/' $PREFIX/etc/snapserver.conf
```

4, 运行`snapserver`
```shell
$ snapserver
```

5，在Termux中播放音乐
这里使用`cmus`，一款终端音乐播放器来实现
```shell
$ pkg i cmus
$ cmus
```
进到`cmus`按数字`5`进到文件浏览界面，找到音乐文件按回车就可以播放了，`cmus`也可以创建歌单等，这里不多赘述
此时访问本机ip的1780端口即可连接到`snapserver`播放音频，另外Snapcast官方亦有提供Android客户端
网址：https://github.com/snapcast/snapcast?tab=readme-ov-file#android-client

6, 自定义Function
总结上面的流程，在`~/.bashrc`写个Function，之后可以通过这个Function来完成一系列操作
```.bashrc
if [[ $BASH = *termux* ]]; then #检查当前是否处于TERMUX环境
  Cmus() {
    pulseaudio --start
    pacmd load-module module-pipe-sink file=$PREFIX/tmp/snapfifo sink_name=Snapcast format=s16le rate=48000
    pacmd update-sink-proplist Snapcast device.description=Snapcast
    pacmd set-default-sink Snapcast
    snapserver &>/dev/null & #运行snapserver且不打印日志
    cmus
    pkill snapserver #退出cmus后顺便停止snapserver
  }
fi
```

后续可通过输入`Cmus`启动`snapserver`跟`cmus`
```shell
$ Cmus
```
