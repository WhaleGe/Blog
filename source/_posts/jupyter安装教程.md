---
title: jupyter安装教程
categories:
  - - tool
    - linux
tags:
  - tool
  - linux
abbrlink: 66d9cc71
date: 2023-05-18 10:12:59
---

### **安装jupyter。如果已经安装好，可忽略此步。**

```
conda create -n jupyter -y
conda activate jupyter
mamba install jupyter -y

```

### **集群终端运行以下命令，生成默认的配置文件，文件在/home/username/.jupyter目录下**

```
jupyter notebook --generate-config

```

### **打开python IDE，并运行以下代码，用于设置jupyter访问密码。记录生成的密钥。**

```
from notebook.auth import passwd
passwd()

```

### **修改配置文件/home/username/.jupyter/jupyter_notebook_config.py**

```
c.NotebookApp.allow_remote_access = True
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.notebook_dir='/path/jupyter ' # 这里的路径是指在浏览器上进入的路径即是jupyter在服务器上的工作路径，在该路径中自己拥有读写权限。
c.NotebookApp.open_browser = False
c.NotebookApp.password='argon2:$argon2id$v=19$m=10240,t=10,p=8$fr1n0n9bTPZZHyriNaVecQ$hIzmU2S5SeJuoPUq7MLCag '  # 这个对应的是jupyter访问密码12345678的密码串，需要写入配置文件
c.NotebookApp.port = 8110 # 可自行指定一个端口，表示jupyter运行的输入输出流都导向此端口。

```

### **在集群登陆节点和测试节点之间进行桥连接。在登陆节点输入如下命令。**

```
ssh -NfL localhost:8135:localhost:8110 yournames@测试节点名称 #修改为自己的用户名和主机名
-N 告诉SSH没有命令要被远程执行；
-f   告诉SSH在后台执行；
-L  是指定port forwarding的配置，远端的端口是第二个localhost后面接的port 8110，本地的端口是第一个localhost后面接的port 8135；

```

### **进入测试节点启动jupyter notebook 并保证该命令不会中断**

```
jupyter notebook
```

### **在本地启动SSH。如果是windows，则先启动cmd**

```
ssh -L 8190:localhost:8135 yournames@登陆节点ip
192.168.61.1表示测试节点的ip
8135表示登录节点用于侦听测试节点所用的端口。
8190表示本地用于侦听登陆节点所用的端口。

ssh -L 8190:localhost:8135 yournames@登陆节点ip
```

### **最后打开浏览器，访问http://localhost:8190/, 之后输入密码（即是指第三步中配置文件中的密码串对应的密码12345678）即可登录。**
