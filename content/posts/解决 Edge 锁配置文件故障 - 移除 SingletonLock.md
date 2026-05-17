---
title: 解决 Edge 锁配置文件故障 - 移除 SingletonLock
description: 
tags: [锁文件, SingletonLock, microsoft-edge]
date: 2025-11-15T08:21:00+08:00
author: NWMA_FYWF
draft: false
---

## 问题现象
在 Fedora 43 GNOME 49 环境下执行 microsoft-edge-stable 启动失败，报错：

``` shell 
XX@XX:~$ microsoft-edge-stable 
[6854:6854:1115/074247.625820:ERROR:chrome/browser/process_singleton_posix.cc:364] 另一计算机(10262)上的另一 Microsoft Edge 进程(192.168.XXX.XX)似乎正在使用此用户配置。Microsoft Edge 已锁定此用户配置以防止损坏。如果你确定没有其他进程正在使用此用户配置，可以将其解锁并重新启动 Microsoft Edge。
[6854:6854:1115/074247.625957:ERROR:chrome/browser/ui/views/message_box_dialog.cc:200] Unable to show message box: Microsoft Edge - 另一计算机(10262)上的另一 Microsoft Edge 进程(192.168.XXX.XX)似乎正在使用此用户配置。Microsoft Edge 已锁定此用户配置以防止损坏。如果你确定没有其他进程正在使用此用户配置，可以将其解锁并重新启动 Microsoft Edge。
XX@XX:~$ 
```

该提示具有误导性——实际无其他设备在用，仅因进程异常退出导致锁文件残留。

## 解决步骤
1. 终止残留进程 （确保无后台运行）：

``` shell 
pkill -f microsoft-edge-stable
```

2. 删除锁文件 （关键操作）：

``` shell 
rm -f ~/.config/microsoft-edge/SingletonLock ~/.config/microsoft-edge/SingletonSocket
```

## 根本原因
~~Microsoft Edge 使用 SingletonLock 和 SingletonSocket 文件实现单实例保护。若进程非正常退出（如崩溃、SSH断开、系统重启），这些文件不会被清除，下次启动即误判为“多机并发”，强制锁定配置目录。~~

简单的总结就是进程异常残留的本地文件锁问题 ~ 
