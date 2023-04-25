

# Adguardhome

[adguard/adguardhome - Docker Image | Docker Hub](https://hub.docker.com/r/adguard/adguardhome#quickstart)



Free and open source, powerful network-wide ads & trackers blocking DNS server.

![img](https://cdn.adguard.com/public/Adguard/Common/adguard_home.gif)

## [Introduction](https://hub.docker.com/r/adguard/adguardhome#introduction)

AdGuard Home is a network-wide software for blocking ads and tracking. After you set it up, it'll cover *all* your home devices, and you won't need any client-side software for that. Learn more on our [official Github repository](https://github.com/AdguardTeam/AdGuardHome).

## [Quick Start](https://hub.docker.com/r/adguard/adguardhome#quickstart)

### Pull the Docker image

This command will pull the latest stable version:

```sh
docker pull adguard/adguardhome
```

### Create directories for persistent configuration and data

The image exposes two volumes for data and configuration persistence. You should create a **data** directory on a suitable volume on your host system, e.g. `/my/own/workdir`, and a **configuration** directory on a suitable volume on your host system, e.g. `/my/own/confdir`.

### Create and run the container

Use the following command to create a new container and run AdGuard Home:

```sh
docker run --name adguardhome\
    --restart unless-stopped\
    -v /my/own/workdir:/opt/adguardhome/work\
    -v /my/own/confdir:/opt/adguardhome/conf\
    -p 53:53/tcp -p 53:53/udp\
    -p 67:67/udp -p 68:68/udp\
    -p 80:80/tcp -p 443:443/tcp -p 443:443/udp -p 3000:3000/tcp\
    -p 853:853/tcp\
    -p 784:784/udp -p 853:853/udp -p 8853:8853/udp\
    -p 5443:5443/tcp -p 5443:5443/udp\
    -d adguard/adguardhome
```

Now you can open the browser and navigate to http://127.0.0.1:3000/ to control your AdGuard Home service.

Don't forget to use your own **data** and **config** directories!

Ports mappings you may need:

- `-p 53:53/tcp -p 53:53/udp`: plain DNS.
- `-p 67:67/udp -p 68:68/tcp -p 68:68/udp`: add if you intend to use AdGuard Home as a DHCP server.
- `-p 80:80/tcp -p 443:443/tcp -p 443:443/udp -p 3000:3000/tcp`: add if you are going to use AdGuard Home's admin panel as well as run AdGuard Home as an [HTTPS/DNS-over-HTTPS](https://github.com/AdguardTeam/AdGuardHome/wiki/Encryption) server.
- `-p 853:853/tcp`: add if you are going to run AdGuard Home as a [DNS-over-TLS](https://github.com/AdguardTeam/AdGuardHome/wiki/Encryption) server.
- `-p 784:784/udp -p 853:853/udp -p 8853:8853/udp`: add if you are going to run AdGuard Home as a [DNS-over-QUIC](https://github.com/AdguardTeam/AdGuardHome/wiki/Encryption) server. You may only leave one or two of these.
- `-p 5443:5443/tcp -p 5443:5443/udp`: add if you are going to run AdGuard Home as a [DNSCrypt](https://github.com/AdguardTeam/AdGuardHome/wiki/DNSCrypt) server.

### Control the container

- Start: `docker start adguardhome`
- Stop: `docker stop adguardhome`
- Remove: `docker rm adguardhome`

## [Update To A Newer Version](https://hub.docker.com/r/adguard/adguardhome#update)

1. Pull the new version from Docker Hub:

   ```sh
   docker pull adguard/adguardhome
   ```

2. Stop and remove currently running container (assuming the container is named `adguardhome`):

   ```sh
   docker stop adguardhome
   docker rm adguardhome
   ```

3. Create and start the container using the new image using the command from the previous section.

## [Running Dev Builds](https://hub.docker.com/r/adguard/adguardhome#unstable)

If you want to be on the bleeding edge, you might want to run the image from the `edge` or `beta` tags. In order to use it, simply replace `adguard/adguardhome` with `adguard/adguardhome:edge` or `adguard/adguardhome:beta` in every command from the quick start. For example:

```sh
docker pull adguard/adguardhome:edge
```

## [Additional Configuration](https://hub.docker.com/r/adguard/adguardhome#configuration)

Upon the first run, a file named `AdGuardHome.yaml` will be created, with default values written into it. You can modify the file while your AdGuard Home container is not running. Otherwise, any changes to the file will be lost because the running program will overwrite them.

Settings are stored in [YAML](https://yaml.org/), possible parameters that you can configure are listed on [this page](https://github.com/AdguardTeam/Adguardhome/wiki/Configuration).

## [DHCP Server](https://hub.docker.com/r/adguard/adguardhome#dhcp)

If you want to use AdGuardHome's DHCP server, you should pass `--network host` argument when creating the container:

```sh
docker run --name adguardhome --network host ...
```

This option instructs Docker to use the host's network rather than a docker-bridged network. Note that port mapping with `-p` is not necessary in this case.

A note from the Docker documentation:

> The host networking driver only works on Linux hosts, and is not supported on Docker Desktop for Mac, Docker Desktop for Windows, or Docker EE for Windows Server.

## [`resolved`](https://hub.docker.com/r/adguard/adguardhome#resolved-daemon)

If you try to run AdGuardHome on a system where the `resolved` daemon is started, docker will fail to bind on port 53, because `resolved` daemon is listening on `127.0.0.53:53`. Here's how you can disable `DNSStubListener` on your machine:

1. Deactivate `DNSStubListener` and update the DNS server address. Create a new file, `/etc/systemd/resolved.conf.d/adguardhome.conf` (creating the `/etc/systemd/resolved.conf.d` directory if needed) and add the following content to it:

   ```none
   [Resolve]
   DNS=127.0.0.1
   DNSStubListener=no
   ```

   Specifying `127.0.0.1` as the DNS server address is necessary because otherwise the nameserver will be `127.0.0.53` which doesn't work without `DNSStubListener`.

2. Activate a new `resolv.conf` file:

   ```sh
   mv /etc/resolv.conf /etc/resolv.conf.backup
   ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
   ```

3. Stop `DNSStubListener`:

   ```sh
   systemctl reload-or-restart systemd-resolved
   ```

Docker Pull Command

```
docker pull adguard/adguardhome
```







## 安装

首先创建一个`work`目录与一个`conf`目录,这两个目录到时候需要映射到docker容器当中

```shell
 mkdir adguard_conf
 mkdir adguard_work
```



以下是经过修改的...因为许多端口冲突了（原版在最上方）

```shell
docker run --name adguardhome\
    --restart unless-stopped\
    -v /usr/adguard_work:/opt/adguardhome/work\
    -v /usr/adguard_conf:/opt/adguardhome/conf\
    -p 53:53/tcp -p 53:53/udp\
    -p 67:67/udp -p 69:68/udp\
    -p 3339:80/tcp -p 443:443/tcp -p 443:443/udp -p 3333:3000/tcp\
    -p 853:853/tcp\
    -p 784:784/udp -p 853:853/udp -p 8853:8853/udp\
    -p 5443:5443/tcp -p 5443:5443/udp\
    -d adguard/adguardhome
```

`3333`初始化配置端口

`3339`控制面板端口

`68`端口由于被占用，所以被我改到了`69`



上面执行时报错发现是53端口被占用。占用进程是`systemd-resolve`

所以我们需要停用`systemd-resolved `服务

```shell
systemctl stop    systemd-resolved
systemctl disable systemd-resolved
systemctl mask    systemd-resolved
```



## 配置

我们需要得到从运营商中获取的DNS

我们使用的openwrt系统的路由，通过SSH登陆到后台中，

查看`log`

```shell
logread


Sat Apr  1 15:14:30 2023 daemon.info pppd[16999]: syncppp not active
Sat Apr  1 15:14:30 2023 daemon.info pppd[16999]: CHAP authentication succeeded: Authentication success,Welcome!
Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: CHAP authentication succeeded
Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: peer from calling number D8:10:9F:02:22:B1 authorized
Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: local  LL address fe80::216e:1742:654e:9b91
Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: remote LL address fe80::da10:9fff:fe02:22b1
Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: local  IP address 100.64.54.77
Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: remote IP address 100.64.0.1
**Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: primary   DNS address 218.30.19.40**
**Sat Apr  1 15:14:30 2023 daemon.notice pppd[16999]: secondary DNS address 61.134.1.4**

上面的DNS就是我们需要的DNS
```



然后将OPENWRT的WAN口DNS改到我们ADGUARD的IP上

网络-DNCP-DNS 转发

网络-接口-WAN-高级选项-使用自定义的 DNS 服务器



### docker ipv6

`docker`默认不开启IPV6，如果我们想要IPV6的DNS还需要开启`docker的ipv6`
