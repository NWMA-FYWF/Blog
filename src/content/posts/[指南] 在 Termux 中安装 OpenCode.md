---
title: "[指南] 在 Termux 中安装 OpenCode"
published: 2026-02-21T14:04:00+08:00
description: '在移动设备便捷使用 OpenCode 之方法'
image: ''
tags: []
category: ''
draft: false
lang: 'zh_CN'
author: NWMA_FYWF
---

## Termux 环境配置
### Termux 更新

``` shell 
pkg upgrade 
```

### proot-distro 安装
由于 OpenCode 官方安装脚本针对标准 Linux 环境设计，我们需要通过 proot-distro 运行完整的 Debian 系统

``` shell 
pkg install proot-distro 
```

查看可用的发行版:

``` shell 
proot-distro list 
```

输出显示支持好多发行版，我选择 Debian (trixie) 以获得最佳的兼容性

## 部署 Debian 容器
下载并安装 rootfs:

``` shell 
proot-distro install debian 
```

### 进入 Debian 环境

``` shell 
proot-distro login debian 
```

成功进入后，提示符变为 `root@localhost:~#`，此时我们已处于完整的 Debian trixie 环境中

## OpenCode 安装
执行官方安装脚本:

``` shell 
curl -fsSL https://opencode.ai/install | bash 
```

安装完成后会显示 OpenCode 的 ASCII Logo

官方提示说已自动添加 PATH，但实际测试发现需要手动 source:

``` shell 
# 验证安装位置
find / -type f -name "opencode" 2>/dev/null 

# 输出为 /root/.opencode/bin/opencode 

```

``` shell 
# 手动添加环境变量
echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.bashrc 
source ~/.bashrc 
```

## OpenCode 启动！

``` shell
opencode 
```

不要忘记为你的项目初始化 OpenCode: 

```
/init 
```

这样我们就可以在 Termux 愉快的进行 Vibe Coding 了~

## 常见问题排查
若键入 opencode 后返回以下内容:

``` shell 
opencode: command not found 
```

你应该检查 ~/.bashrc 中 PATH 配置，然后执行:

``` shell 
source ~/.bashrc 
```

## 参考链接
- Termux 官网：https://termux.dev
- Termux 仓库：https://github.com/termux/termux-app
- proot-distro 仓库：https://github.com/termux/proot-distro
- PRoot 维基：https://wiki.termux.com/wiki/PRoot
- OpenCode 官网：https://opencode.ai
- OpenCode 文档：https://opencode.ai/docs
 