

首先我们去他的官网申请一个网络ID

然后将他的网络ID保存备用:`db64858fed274441`

网站还可以选择虚拟局域网网段



首先查看我们的`linux`环境当中是否有`tun`环境

然后查看一下他的权限

```shell
ll /dev/net/tun
```

如果他的权限只有ROOT的话，那么我们需要将他设置为所有人可读写

```shell
chmod 666 /dev/net/tun
```

并且我们再写一个脚本每次开机启动的时候都对该文件夹进行权限修改

```shell
# 编辑脚本文件
vim /etc/rc.d/S50tun

#脚本内容
#!/bin/sh -e
insmod /lib/modules/tun.ko || true
chmod 666 /dev/net/tun

#赋予执行权限
chmod a+x /etc/rc.d/S50tun

#执行一遍
/etc/rc.d/S50tun
```



然后创建一个目录给`zerotier`使用：

```shell
mkdir /usr/zerotier_docker
```



执行：

```shell
docker run --name zerotier \
  -d \
  --restart always \
  --net host \
  --cap-add NET_ADMIN \
  --device /dev/net/tun \
  -v /usr/zerotier_docker:/var/lib/zerotier-one \
  zerotier/zerotier:latest \
  db64858fed274441  #你的网络ID
```

