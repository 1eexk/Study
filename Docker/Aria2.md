[p3terx/aria2-pro - Docker Image | Docker Hub](https://hub.docker.com/r/p3terx/aria2-pro)

docker-compoese

```shell
version: "3.8"

services:

  Aria2-Pro:
    container_name: aria2-pro
    image: p3terx/aria2-pro:latest
    environment:
      - PUID=0
      - PGID=0
      - UMASK_SET=022
      - RPC_SECRET=P3TERX
      - RPC_PORT=6800
      - LISTEN_PORT=6888
      - DISK_CACHE=64M
      - IPV6_MODE=false
      - UPDATE_TRACKERS=true
      - CUSTOM_TRACKER_URL=
      - TZ=Asia/Shanghai
    volumes:
      - ${PWD}/aria2-config:/config
      - ${PWD}/aria2-downloads:/downloads
# If you use host network mode, then no port mapping is required.
# This is the easiest way to use IPv6 networks.
#    network_mode: host
    network_mode: bridge
    ports:
      - 6800:6800
      - 6888:6888
      - 6888:6888/udp
    restart: unless-stopped
# Since Aria2 will continue to generate logs, limit the log size to 1M to prevent your hard disk from running out of space.
    logging:
      driver: json-file
      options:
        max-size: 1m

# AriaNg is just a static web page, usually you only need to deploy on a single host.
  AriaNg:
    container_name: ariang
    image: p3terx/ariang:latest
    command: --port 6880 --ipv6
#    network_mode: host
    network_mode: bridge
    ports:
      - 6880:6880
    restart: unless-stopped
    logging:
      driver: json-file
      options:
        max-size: 1m
```



## Parameters

| Parameter                      | Function                                                     |
| ------------------------------ | ------------------------------------------------------------ |
| `-e PUID=$UID` `-e PGID=$GID`  | Bind UID and GID to the container, which means you can use a non-root user to manage downloaded files. |
| `-e UMASK_SET=022`             | For umask setting of Aria2, optional , default if left unset is `022` |
| `-e RPC_SECRET=<TOKEN>`        | Set RPC secret authorization token. Default: `P3TERX`        |
| `-e RPC_PORT=6800`             | Set RPC listen port.                                         |
| `-p 6800:6800`                 | bind RPC listen port.                                        |
| `-e LISTEN_PORT=6888`          | Set TCP/UDP port number for BitTorrent/DHT listen.           |
| `-p 6888:6888`                 | Bind BT listen port (TCP).                                   |
| `-p 6888:6888/udp`             | Bind DHT lisen port (UDP).                                   |
| `-v <PATH>:/config`            | Contains all relevant configuration files.                   |
| `-v <PATH>:/downloads`         | Location of downloads on disk.                               |
| `-e DISK_CACHE=<SIZE>`         | Set up disk cache. SIZE can include `K` or `M` (1K = 1024, 1M = 1024K), e.g `64M`. |
| `-e IPV6_MODE=<BOOLEAN>`       | Whether to enable IPv6 support for Aria2. Optional: `true` or `false`. Set the options `disable-ipv6=false` and `enable-dht6=true` in the configuration file(aria2.conf). |
| `-e UPDATE_TRACKERS=<BOOLEAN>` | Whether to update BT Trackers List automatically. Optional: `true` or `flase`, default if left unset is `true` |
| `-e CUSTOM_TRACKER_URL=<URL>`  | Custom BT Trackers List URL. If not set, it will be get from https://trackerslist.com/all_aria2.txt. |
| `-e TZ=Asia/Shanghai`          | Specify a timezone to use e.g. `Asia/Shanghai`               |

