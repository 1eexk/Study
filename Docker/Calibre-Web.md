### 使用docker-compose搭建

选择一个目录作为映射使用

进入目录当中，创建一个`docker-compose.yaml`文件

```shell
vim docker-compose.yaml
```



```shell
---
version: "2.1"
services:
  calibre-web:
    image: lscr.io/linuxserver/calibre-web:latest
    container_name: calibre-web
    environment:
      - PUID=0
      - PGID=0
      - TZ=Asia/Shanghai
      - DOCKER_MODS=linuxserver/mods:universal-calibre #optional
      - OAUTHLIB_RELAX_TOKEN_SCOPE=1 #optional
    volumes:
      - ./data:/config
      - ./library:/books
    ports:
      - 8083:8083
    restart: unless-stopped
```

```
以root身份来运行的，所以这边的PUID=0，PGID=0
```

然后等待容器搭建成功，然后下载一个空的数据库到容器`/books`目录所对应的映射目录下。(空的数据库可以通过安装PC版的`calibre`生成)。



之后配置豆瓣源[群晖NAS使用最新calibre-web并配置豆瓣元数据 – Gary的个人技术记录 (fugary.com)](https://fugary.com/?p=238)

[fugary/calibre-web-douban-api: 新版calibre-web已经移除douban-api了，添加一个豆瓣api实现 (github.com)](https://github.com/fugary/calibre-web-douban-api)

下载[NewDouban.py](https://github.com/fugary/calibre-web-douban-api/releases/download/v1.1.0/NewDouban.py)，然后将其移到`calibre-web/cps/metadata_provider/`目录下（将该目录下的`.py`全部删除），重启项目即可。