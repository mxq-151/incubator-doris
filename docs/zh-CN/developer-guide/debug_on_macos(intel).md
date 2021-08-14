本教程基于macos 10.14演示编译incubator-doris 0.14版本，以下是所需软件环境
1. docker v20.10.7
2. docker-mac-network(github:https://github.com/wojas/docker-mac-network)
3. doris在docker当中编译环境：apache/incubator-doris:build-env-1.2
4. clion(2021.1)

环境搭建原理是在基于clion的远程开发调试功能连接docker中的编译环境（要求能从自身的macos上ssh过去），从而达到在macos上开发apache doris的目的，但如果在本机无法直接ping通docker容器中的网络，需要借助vpn（docker-mac-network）实现网络连通；下面讲述基于你已了解docker的基本使用，重点讲述使用vpn连通docker和clion配置开发环境


1. 部署docker与macos的网络服务
请查看https://github.com/wojas/docker-mac-network 的使用说明，在此过程中可能会遇到compression error,需要在docker-for-mac.ovpn这个文件中添加comp-lzo,结果如下
```
client
nobind
dev tun
remote-cert-tls server

remote localhost 13194 tcp
comp-lzo

<key>
-----BEGIN PRIVATE KEY-----
.
.
.
```

2. 在docker中安装sshd服务（请不要轻易改docker器器的IP，除非你熟悉网络原理）
首先进入docker容器运行环境
```
docker run -it -v /~/.m2:/root/.m2 apache/incubator-doris:build-env-1.2

yum install -y openssh-server

touch /run.sh

###添加启动ssh脚本
#!/bin/bash
/usr/sbin/sshd -D &

###为root添加密码
passwd root
```

3. 配置clion连接docker
![image](https://user-images.githubusercontent.com/12026157/129444338-c343a797-35eb-475e-bc17-10f96a983a82.png)


4. 配置环境参数
![image](https://user-images.githubusercontent.com/12026157/129444699-dcfd690f-adb3-4f17-82a6-fe59fce87fa3.png)
```
DORIS_GCC_HOME=/usr;DORIS_THIRDPARTY=/var/local/thirdparty
```

5.配置doris在docker中的运行目录
![image](https://user-images.githubusercontent.com/12026157/129444802-59f12b67-071f-4f1d-aa10-50359491a142.png)
![image](https://user-images.githubusercontent.com/12026157/129444825-52600bbf-3f4c-4bf6-b627-505238b4b1dd.png)


6. 配置运行环境参数
```
DORIS_GCC_HOME=/usr;DORIS_THIRDPARTY=/var/local/thirdparty
```
![image](https://user-images.githubusercontent.com/12026157/129444858-4cc4694d-c013-485a-8165-986d48da75d3.png)


7. 上传代码
![image](https://user-images.githubusercontent.com/12026157/129444930-9b4fc9ea-153e-4022-82d1-cede6c82f0bd.png)

8. 执行cmake3
![image](https://user-images.githubusercontent.com/12026157/129445133-ab09c944-b3d4-483d-84b0-d3fb14a61cad.png)

9. 运行debug
![image](https://user-images.githubusercontent.com/12026157/129445153-cef50149-5c0e-4e8e-91dc-b92d057b2f16.png)
