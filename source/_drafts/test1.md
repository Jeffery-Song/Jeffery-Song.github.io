---
layout: post
title: 常用环境配置
date: 2019-08-02 15:31:49
tags:
---

这里记录了一些我常用的环境配置内容

## 使用`PageUp`、`PageDown`进行指令历史的前缀匹配
将以下内容添加到`~/.inputrc`中

```bash
"\e[5~": history-search-backward
"\e[6~": history-search-forward
```

## Tmux相关
将一下内容添加到`~/.tmux.conf`中。对于已经打开的tmux实例，使用指令`source-file ~/.tmux.conf`来启用这些配置
```
# set the prefix to Ctrl+a
set -g prefix C-a

# enable color_prompt
set -g default-terminal "screen-256color"
```

## Environment Variables
```bash
install_dir=<folder name in local>
export PATH=/home/jeffery/local/$install_dir/bin:$PATH
export LD_LIBRARY_PATH=/home/jeffery/local/$install_dir/lib:$LD_LIBRARY_PATH
export LIBRARY_PATH=/home/jeffery/local/$install_dir/lib:$LIBRARY_PATH
export C_INCLUDE_PATH=/home/jeffery/local/$install_dir/include:$C_INCLUDE_PATH
export CPLUS_INCLUDE_PATH=/home/jeffery/local/$install_dir/include:$CPLUS_INCLUDE_PATH
```