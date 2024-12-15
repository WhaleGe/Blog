---
title: tmux安装教程
categories:
  - - tool
    - linux
tags:
  - tool
  - linux
abbrlink: 686264ec
date: 2023-05-18 10:13:10
---

## tmux 简介

tmux 是一个 terminal multiplexer（终端复用器），它可以启动一系列终端会话。
简单来说，安装 tmux 之前，一旦**与服务器断开连接**或者**关闭 xhell 或其他 shell 终端**，我们的服务器上运行的程序就会终止，而且输入的历史消息全部消失。因此如果我们希望整晚在服务器上跑代码，我们的电脑也要整晚一直连接着服务器。而安装了 tmux 之后，即使我们关闭了 shell 终端或者不幸与服务器断开连接，我们在服务器上的程序**依然在运行**。

## tmux 安装

* root 用户安装一行命令

    ```bash
    sodu apt-get install tmux
    ```

* 非 root 用户需要下载源码安装

    * **下载**：

        ```bash
        ### 下载 tmux 及其依赖软件
        wget -c https://github.com/tmux/tmux/releases/download/3.0a/tmux-3.0a.tar.gz
        wget -c https://github.com/libevent/libevent/releases/download/release-2.1.11-stable/libevent-2.1.11-stable.tar.gz
        wget -c https://ftp.gnu.org/gnu/ncurses/ncurses-6.2.tar.gz
        ```

    * **解压**

        ```bash
        tar -xzvf tmux-3.0a.tar.gz
        tar -xzvf libevent-2.1.11-stable.tar.gz
        tar -xzvf ncurses-6.2.tar.gz
        ```

    * **分别源码安装，先安装两个依赖包**

        * libevent 会安在 /tmux_depend / lib

            ```bash
            cd  libevent-2.1.11-stable
            #$HOME/tmux_depend是我的安装路径，大家可以修改
            ./configure --prefix=/path/Tmux/tmux_depend --disable-shared
            make && make install
            ```

        * ncurses 会安在 /tmux_depend / include

            ```bash
            cd  ncurses-6.2
            ./configure --prefix=/path/Tmux/tmux_depend --disable-shared
            make && make install
            ```

    * **安装 tmux**

        ```bash
        cd  tmux-3.3a
        ./configure CFLAGS="-I/path/Tmux/tmux_depend/include -I/path/Tmux/tmux_depend/include/ncurses" LDFLAGS="-L/path/Tmux/tmux_depend/lib -L/path/Tmux/tmux_depend/include/ncurses -L/path/Tmux/tmux_depend/include"
        make
        cp tmux  /path/Tmux/tmux_depend/bin
        ```

## 设置环境变量

```bash
export PATH=$HOME/tmux_depend/bin:$PATH
source ~/.bashrc
```

## tmux 常用命令

```bash
### 新建会话，比如新创建一个会话以"ccc"命名 tmn
tmux new -s ccc
加上参数-d，表示在后台新建会话
tmux new -s shibo -d

###　查看创建得所有会话 tml
tmux ls

### 登录一个已知会话,即从终端环境进入会话,第一个参数a也可以写成attach。后面的aaa是会话名称。tma
tmux a -t aaa 

### 退出会话不是关闭：
登到某一个会话后，先按键ctrl+b启动快捷键，再按d，这样就会退出该会话，但不会关闭会话。
如果直接ctrl + d，就会在退出会话的通话也关闭了该会话！
 
### 关闭会话（销毁会话） tmk
tmux kill-session -t bbb

### 重命名会话
tmux rename -t wangshibo kevin
```
