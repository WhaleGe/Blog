---
title: code-server安装教程
categories:
  - - tool
    - linux
tags:
  - tool
  - linux
abbrlink: 4c46dc20
date: 2023-05-18 10:12:33
---

## 下载


* 官方的项目`Github`地址在这 [coder/code-server](https://github.com/coder/code-server)

* 在`Releases`=>`Assets`里面找`linux-amd`包 ，可以本地下载再上传到服务器端

* 在`Linux`终端输入命令下载

    ```bash
    wget https://github.com/coder/code-server/releases/download/v4.5.1/code-server-4.5.1-linux-amd64.tar.gz
    ```

## 安装

```bash
# 在终端安装，解压就行
tar -xzvf code-server-4.5.1-linux-amd64.tar.gz
```

## 启动

```bash
cd bin
./code-server
```

就会出现如下界面启动了

```txt
[2022-03-07T11:13:55.467Z] info  code-server 4.1.0 9e620e90f53fb91338a2ba1aaa2e556d42ae52d5
[2022-03-07T11:13:55.468Z] info  Using user-data-dir ~/.local/share/code-server
[2022-03-07T11:13:55.479Z] info  Using config file ~/.config/code-server/config.yaml
[2022-03-07T11:13:55.479Z] info  HTTP server listening on http://127.0.0.1:888/ 
[2022-03-07T11:13:55.479Z] info    - Authentication is enabled
[2022-03-07T11:13:55.479Z] info      - Using password from ~/.config/code-server/config.yaml
[2022-03-07T11:13:55.479Z] info    - Not serving HTTPS
```

可以看到配置文件在`~/.config/code-server/config.yaml`这里  

## 配置

修改配置文件

```bash
vi ~/.config/code-server/config.yaml

### config.yaml内容可以修改成如下(端口和密码需要自己改成其它)
bind-addr: 0.0.0.0:8446
auth: password
password: 12345678
cert: false
```

## 穿透

```bash
### 端口可以随意修改
### 在登陆节点执行下述命令
ssh -NfL localhost:8447:localhost:8446 yourname@测试节点名称
### 在本地cmd中执行下述命令
ssh -L 8448:localhost:8447 yourname@登陆节点ip
### 最后在浏览器打开对应网址
http://localhost:8448/
```

## 运行

如需持续运行，可以再安装[tmux](tmux.md)程序

```bash
tmux new -s vscode
./code-server
# 使用快捷键Ctrl+b, 再按d退出即可
```
