[docker对已经启动的容器添加目录映射(挂载目录)](https://blog.csdn.net/qq_39198749/article/details/127731024)



我们要新增宿主目录`/usr/docker_data/aria2/aria2-downloads`映射到`alist`容器的`/media/usb`

*   查看容器存放目录

```shell
root@renegade:/etc/alist# docker info | grep "Root"
 Docker Root Dir: /var/lib/docker
```

*   查看要新增挂载的容器id,我使用的`portainer`看的，也可以使用`docker ps`查看
*   进入要新增文件夹挂载的容器的目录

```shell
root@renegade:/etc/alist# cd /var/lib/docker
root@renegade:/var/lib/docker# ls
buildkit  containers  engine-id  image  network  overlay2  plugins  runtimes  swarm  tmp  volumes
root@renegade:/var/lib/docker# cd containers/
root@renegade:/var/lib/docker/containers# ls
0796ba3f2483735c6e382a47a8180d99f15ff6ae41d979473d957c0e2715dcbc  9b772f49372ccf68fda2373ab8ad21a40a733030f42bb4a25e5985161d657037
32bd13071c7520dc2b1a45bf5e0978eb69b041fc83a9fbd5a26d3a9853cd3f12  a711fe66e55ab41b5205c37f37ac9149ff97cee782f1ebe9d3837c1945cdb877
5df16267ab4f4fe10ed88924f8614d99fc5632d01e79491b380f86bea4b32aee  bbed78d776d453c00c0d76d13e54b0ff4380bd91e4571911ddad15c59ac6b0e9
747caa01b751cb24f0ffc2fe21dad3600d9c77e6a2a206bb5742eea7be198b55  e91b9c26b6d82ab4dc4401c7398b5f76565d937c9eda519b18ec8db9f4fb518d
root@renegade:/var/lib/docker/containers# cd 5df16267ab4f4fe10ed88924f8614d99fc5632d01e79491b380f86bea4b32aee/
root@renegade:/var/lib/docker/containers/5df16267ab4f4fe10ed88924f8614d99fc5632d01e79491b380f86bea4b32aee# ls
5df16267ab4f4fe10ed88924f8614d99fc5632d01e79491b380f86bea4b32aee-json.log  config.v2.json   hostname  mounts       resolv.conf.hash
checkpoints                                                                hostconfig.json  hosts     resolv.conf
root@renegade:/var/lib/docker/containers/5df16267ab4f4fe10ed88924f8614d99fc5632d01e79491b380f86bea4b32aee#


```

我们将要修改的文件就是`config.v2.json`和`hostconfig.json`

*   关闭容器和`docker`

```shell
systemctl stop docker
```

*   修改`config.v2.json`

说明：想要将容器中的`/media/usb`目录映射到宿主机的`/usr/docker_data/aria2/aria2-downloads`目录下。
在`config.v2.json`文件中找到`MountPoints`，添加如下`/media/usb`的映射即可(这里如果前面还有内容的话，注意逗号)

```shell
{
    ......
	"MountPoints": {
		......,
        "/media/usb":{
            "Source":"/usr/docker_data/aria2/aria2-downloads",
            "Destination":"/media/usb",
            "RW":true,
            "Name":"",
            "Driver":"",
            "Type":"bind",
            "Propagation":"rprivate",
            "Spec":{
                "Type":"bind",
                "Source":"/usr/docker_data/aria2/aria2-downloads",
                "Target":"/opt/alist/data"
            },
            "SkipMountpointCreation":false
		}
	},
	
	......
	
}
```

*   修改`hostconfig.json`

```shell
{
	"Binds": [
		......,
		"/usr/docker_data/aria2/aria2-downloads:/media/usb"
	],
	
	......
	
}
```

*   重启docker与容器

```shell
systemctl start docker
```

