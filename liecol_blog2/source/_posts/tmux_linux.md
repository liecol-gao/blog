---
title: 多窗口工具tmux快捷键速查表
date: 2017-07-10 13:12:40
tags: [tmux,linux]
categories:
- linux
---

## 介绍：
tmux 是在一个终端进行多窗口回话的工具，可在后台运行，进入、断开会话。
如下图示例：

{% img "" /images/tmux_001.png 700 280 liecol-晓斌|tmux liecol-晓斌|tmux %}

## tmux的快捷键前缀：
* 按下组合键 Ctrl+b (Tmux 快捷键前缀)
* 放开组合键 Ctrl+b
* 按下 tmux内部指令
<!--more-->

## 安装tmux
笔者以Ubuntu为例：
```
sudo apt-get install tmux
```
## 修改tmux数据配置：
--路径:~/.tmux.conf
```
前缀绑定 (Ctrl+a)
set -g prefix ^a
unbind ^b
bind a send-prefix

居中窗口列表
set -g status-justify centre

最大化/恢复窗格
unbind Up bind Up new-window -d -n tmp \; swap-pane -s tmp.1 \; select-window -t tmp
unbind Down
bind Down last-window \; swap-pane -s tmp.1 \; kill-window -t tmp
```

## tmux会话快捷键:
```
启动新会话
tmux new -s session-name
eg: tmux new -s liecol-0

列出所有会话：
tmux ls
create By liecol-晓斌

恢复会话：
tmux at [-t 会话名]
tmux a -t session-name
eg: tmux at -t liecol

强制独占恢复会话：
tmux at [-dt 会话名]
tmux at -dt liecol

删除会话
tmux kill-session -t session-name
tmux kill-session -t liecol1
```

## 窗口&窗格杂项：
```
s 列出所有会话 可用上下键进入对应会话
$ 重命名当前的会话
d 断开当前的会话

窗口 (标签页)
c  创建新窗口
w  列出所有窗口
n  后一个窗口
p  前一个窗口
f  查找窗口
,  重命名当前窗口
&  关闭当前窗口

窗格
% 水平分割窗口
" 竖直分割窗口
} 与下一个窗格交换位置
{ 与上一个窗格交换位置
将光标移入对应窗口 上下左右箭头
t 钟表时间
```

