`docker-compose.yml`:

```shell
version: '3.1'
services:
  reader:
    image: hectorqin/reader:openj9-latest #docker镜像，arm64架构或小内存机器优先使用此镜像.启用需删除上一行
    container_name: myreader #容器名 可自行修改
    restart: always
    ports:
      - 4396:8080 #4396端口映射可自行修改
    networks:
      - share_net
    volumes:
      - ./logs:/logs #log映射目录 
      - ./storage:/storage #数据映射目录
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - READER_APP_USERLIMIT=10 #用户上限,默认50
      - READER_APP_USERBOOKLIMIT=300 #用户书籍上限,默认200
      - READER_APP_CACHECHAPTERCONTENT=true #开启缓存章节内容 V2.0
      # 下面都是多用户模式配置
      - READER_APP_SECURE=true #开启登录鉴权，开启后将支持多用户模式
      - READER_APP_SECUREKEY=521kai  #管理员密码  建议修改
      # - READER_APP_INVITECODE=registercode #注册邀请码 建议修改,如不需要可注释或删除
# 自动更新docker镜像
  watchtower:
    image: containrrr/watchtower
    container_name: watchtower
    restart: always
    # 环境变量,设置为上海时区
    environment:
        - TZ=Asia/Shanghai
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: reader watchtower --cleanup --schedule "0 0 4 * * *"
    networks:
      - share_net
    # 仅更新reader与watchtower容器,如需其他自行添加 '容器名' ,如:reader watchtower nginx
    # --cleanup 更新后清理旧版本镜像
    # --schedule 自动检测更新 crontab定时(限定6位crontab) 此处代表凌晨4点整
networks:
  share_net:
    driver: bridge
```



该容器需要自己注册用户。

填写的密码只是管理员密码，但是没有管理员账户..任何一个用户输入管理员密码都可以有管理员权限。
