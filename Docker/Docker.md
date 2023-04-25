## Docker入门

### 安装docker

通过`armbian-config`自动安装`docker`。

`docker`的基础指令:[Docker实践(二)：容器的管理(创建、查看、启动、终止、删除)_docker](https://blog.csdn.net/u010246789/article/details/53958662)



#### 安装Portainer面板

```shell
docker pull portainer/portainer:latest
```



单机运行：

```shell
docker run -d -p 9000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data --name prtainer-libai portainer/portainer
```

[docker可视化管理——Portainer安装教程_portainer 安装_qq_34528463的博客-CSDN博客](https://blog.csdn.net/qq_34528463/article/details/106687234)

参数说明：
-d：容器在后台运行；
-p 9000:9000 ：宿主机9000端口映射容器中的9000端口（前面的是宿主机端口，后面的是容器端口）；
–restart 标志会检查容器的退出代码，并据此来决定是否要重启容器，默认不会重启；
–restart=always：自动重启该容器；
-v /var/run/docker.sock:/var/run/docker.sock ：把宿主机的Docker守护进程(Docker daemon)默认监听的Unix域套接字挂载到容器中；
-v portainer_data:/data ：把宿主机portainer_data数据卷挂载到容器/data目录；
–name prtainer-test ： 给容器起名为portainer-libai；





**安装面板报错**：

**docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: unable to apply apparmor profile: apparmor failed to apply profile: write /proc/self/attr/apparmor/exec: no such file or directory: unknown.**

[docker 无法安装portainer以及FAST面板](https://ask.csdn.net/questions/7885764)

[ubuntu安装mysql时报错缺少apparmor的依赖_ubuntu apparmor 不存在](https://blog.csdn.net/qq_38522539/article/details/80638003)

解决：

容器进程无法应用 AppArmor 配置文件。这是因为系统上的 AppArmor 存在问题，或者容器缺少了某些必要的资源。
解决这个问题可以试试：
1.检查系统是否安装了 AppArmor，并确保它处于启用状态。



建议先使用statua命令查看apparmor的运行状态，若提示找不到命令，说明你的[ubuntu](https://so.csdn.net/so/search?q=ubuntu&spm=1001.2101.3001.7020)缺少该内核模块

```shell
sudo apparmor_status
```

安装请执行该命令，这命令会把profiles和utils的相关软件也一并安装：

```shell
sudo apt-get install apparmor
```





2.如果 AppArmor 被禁用了，请使用以下命令启用它：

```shell
sudo systemctl enable apparmor
sudo systemctl start apparmor
```

3.重启 Docker 守护程序，以重新加载 AppArmor 配置文件：

```
sudo systemctl restart docker
```

4.一二三都解决不了就清除 Docker 容器和镜像，并重新运行容器。这可以通过以下命令实现：

```
sudo docker system prune -a
sudo docker run <image_name>
```





#### 安装导航页

`soulteary/flare`

```shell
docker run --name myflare \
	-d \
	--restart always \
	-v /usr/flare/app:/app \
	-p 5005:5005 \
	soulteary/flare:latest
```



#### 修改Docker配置（可选）（来自烧饼博客）

以下配置会增加一段自定义内网 IPv6 地址，开启容器的 IPv6 功能，以及限制日志文件大小，防止 Docker 日志塞满硬盘（泪的教训）：

```javascript
cat > /etc/docker/daemon.json <<EOF
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "20m",
        "max-file": "3"
    },
    "ipv6": true,
    "fixed-cidr-v6": "fd00:dead:beef:c0::/80",
    "experimental":true,
    "ip6tables":true
}
EOF
```

然后重启 Docker 服务：

```shell
systemctl restart docker
```
