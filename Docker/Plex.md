[Plex](https://plex.tv/) organizes video, music and photos from personal media libraries and streams them to smart TVs, streaming boxes and mobile devices. This container is packaged as a standalone Plex Media Server. has always been a top priority. Straightforward design and bulk actions mean getting things done faster.

[![plex](http://the-gadgeteer.com/wp-content/uploads/2015/10/plex-logo-e1446990678679.png)](https://plex.tv/)

## Supported Architectures

We utilise the docker manifest for multi-platform awareness. More information is available from docker [here](https://github.com/docker/distribution/blob/master/docs/spec/manifest-v2-2.md#manifest-list) and our announcement [here](https://blog.linuxserver.io/2019/02/21/the-lsio-pipeline-project/).

Simply pulling `lscr.io/linuxserver/plex:latest` should retrieve the correct image for your arch, but you can also pull specific arch images via tags.

The architectures supported by this image are:

| Architecture | Available | Tag                   |
| ------------ | --------- | --------------------- |
| x86-64       | ✅         | amd64-<version tag>   |
| arm64        | ✅         | arm64v8-<version tag> |
| armhf        | ✅         | arm32v7-<version tag> |

## Application Setup

Webui can be found at `<your-ip>:32400/web`

** Note about updates, if there is no value set for the VERSION variable, then no updates will take place.**

** For new users, no updates will take place on the first run of the container as there is no preferences file to read your token from, to update restart the Docker container after logging in through the webui**

Valid settings for VERSION are:-

```
IMPORTANT NOTE:- YOU CANNOT UPDATE TO A PLEXPASS ONLY (BETA) VERSION IF YOU ARE NOT LOGGED IN WITH A PLEXPASS ACCOUNT
```

-   **`docker`**: Let Docker handle the Plex Version, we keep our Dockerhub Endpoint up to date with the latest public builds. This is the same as leaving this setting out of your create command.
-   **`latest`**: will update plex to the latest version available that you are entitled to.
-   **`public`**: will update plexpass users to the latest public version, useful for plexpass users that don't want to be on the bleeding edge but still want the latest public updates.
-   **`<specific-version>`**: will select a specific version (eg 0.9.12.4.1192-9a47d21) of plex to install, note you cannot use this to access plexpass versions if you do not have plexpass.

## Hardware Acceleration

### Intel

Hardware acceleration users for Intel Quicksync will need to mount their /dev/dri video device inside of the container by passing the following command when running or creating the container:

```
--device=/dev/dri:/dev/dri
```

We will automatically ensure the abc user inside of the container has the proper permissions to access this device.

### Nvidia

Hardware acceleration users for Nvidia will need to install the container runtime provided by Nvidia on their host, instructions can be found here:

https://github.com/NVIDIA/nvidia-docker

We automatically add the necessary environment variable that will utilise all the features available on a GPU on the host. Once nvidia-docker is installed on your host you will need to re/create the docker container with the nvidia container runtime `--runtime=nvidia` and add an environment variable `-e NVIDIA_VISIBLE_DEVICES=all` (can also be set to a specific gpu's UUID, this can be discovered by running `nvidia-smi --query-gpu=gpu_name,gpu_uuid --format=csv` ). NVIDIA automatically mounts the GPU and drivers from your host into the plex docker.

## Usage

Here are some example snippets to help you get started creating a container.

### docker-compose (recommended, [click here for more info](https://docs.linuxserver.io/general/docker-compose))

```yaml
---
version: "2.1"
services:
  plex:
    image: lscr.io/linuxserver/plex:latest
    container_name: plex
    network_mode: host
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - VERSION=docker
      - PLEX_CLAIM= #optional
    volumes:
      - /path/to/library:/config
      - /path/to/tvseries:/tv
      - /path/to/movies:/movies
    restart: unless-stopped
```

### docker cli ([click here for more info](https://docs.docker.com/engine/reference/commandline/cli/))

```bash
docker run -d \
  --name=plex \
  --net=host \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Etc/UTC \
  -e VERSION=docker \
  -e PLEX_CLAIM= `#optional` \
  -v /path/to/library:/config \
  -v /path/to/tvseries:/tv \
  -v /path/to/movies:/movies \
  --restart unless-stopped \
  lscr.io/linuxserver/plex:latest
```

## Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter           | Function                                                     |
| ------------------- | ------------------------------------------------------------ |
| `--net=host`        | Use Host Networking                                          |
| `-e PUID=1000`      | for UserID - see below for explanation                       |
| `-e PGID=1000`      | for GroupID - see below for explanation                      |
| `-e TZ=Etc/UTC`     | specify a timezone to use, see this [list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List). |
| `-e VERSION=docker` | Set whether to update plex or not - see Application Setup section. |
| `-e PLEX_CLAIM=`    | Optionally you can obtain a claim token from https://plex.tv/claim and input here. Keep in mind that the claim tokens expire within 4 minutes. |
| `-v /config`        | Plex library location. *This can grow very large, 50gb+ is likely for a large collection.* |
| `-v /tv`            | Media goes here. Add as many as needed e.g. `/movies`, `/tv`, etc. |
| `-v /movies`        | Media goes here. Add as many as needed e.g. `/movies`, `/tv`, etc. |

## Environment variables from files (Docker secrets)

You can set any environment variable from a file by using a special prepend `FILE__`.

As an example:

```bash
-e FILE__PASSWORD=/run/secrets/mysecretpassword
```

Will set the environment variable `PASSWORD` based on the contents of the `/run/secrets/mysecretpassword` file.

## Umask for running applications

For all of our images we provide the ability to override the default umask settings for services started within the containers using the optional `-e UMASK=022` setting. Keep in mind umask is not chmod it subtracts from permissions based on it's value it does not add. Please read up [here](https://en.wikipedia.org/wiki/Umask) before asking for support.

## Optional Parameters

If you want to run the container in bridge network mode (instead of the recommended host network mode) you will need to specify ports. The [official documentation for ports](https://support.plex.tv/articles/201543147-what-network-ports-do-i-need-to-allow-through-my-firewall/) lists 32400 as the only required port. The rest of the ports are optionally used for specific purposes listed in the documentation. If you have not already claimed your server (first time setup) you need to set `PLEX_CLAIM` to claim a server set up with bridge networking.

```
  -p 32400:32400 \
  -p 1900:1900/udp \
  -p 3005:3005 \
  -p 5353:5353/udp \
  -p 8324:8324 \
  -p 32410:32410/udp \
  -p 32412:32412/udp \
  -p 32413:32413/udp \
  -p 32414:32414/udp \
  -p 32469:32469
```

The application accepts a series of environment variables to further customize itself on boot:

| Parameter                    | Function                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| `--device=/dev/dri:/dev/dri` | Add this option to your run command if you plan on using Quicksync hardware acceleration - see Application Setup section. |
| `--device=/dev/dvb:/dev/dvb` | Add this option to your run command if you plan on using dvb devices. |

## User / Group Identifiers

When using volumes (`-v` flags) permissions issues can arise between the host OS and the container, we avoid this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1000` and `PGID=1000`, to find yours use `id user` as below:

```bash
  $ id username
    uid=1000(dockeruser) gid=1000(dockergroup) groups=1000(dockergroup)
```

## Docker Mods

[![Docker Mods](https://img.shields.io/badge/dynamic/yaml?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=plex&query=%24.mods%5B%27plex%27%5D.mod_count&url=https%3A%2F%2Fraw.githubusercontent.com%2Flinuxserver%2Fdocker-mods%2Fmaster%2Fmod-list.yml)](https://mods.linuxserver.io/?mod=plex) [![Docker Universal Mods](https://img.shields.io/badge/dynamic/yaml?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=universal&query=%24.mods%5B%27universal%27%5D.mod_count&url=https%3A%2F%2Fraw.githubusercontent.com%2Flinuxserver%2Fdocker-mods%2Fmaster%2Fmod-list.yml)](https://mods.linuxserver.io/?mod=universal)

We publish various [Docker Mods](https://github.com/linuxserver/docker-mods) to enable additional functionality within the containers. The list of Mods available for this image (if any) as well as universal mods that can be applied to any one of our images can be accessed via the dynamic badges above.

## Support Info

-   Shell access whilst the container is running: `docker exec -it plex /bin/bash`
-   To monitor the logs of the container in realtime: `docker logs -f plex`
-   container version number
    -   `docker inspect -f '{{ index .Config.Labels "build_version" }}' plex`
-   image version number
    -   `docker inspect -f '{{ index .Config.Labels "build_version" }}' lscr.io/linuxserver/plex:latest`

## Updating Info

Most of our images are static, versioned, and require an image update and container recreation to update the app inside. With some exceptions (ie. nextcloud, plex), we do not recommend or support updating apps inside the container. Please consult the [Application Setup](https://hub.docker.com/r/linuxserver/plex#application-setup) section above to see if it is recommended for the image.

Below are the instructions for updating containers:

### Via Docker Compose

-   Update all images:

     

    ```
    docker-compose pull
    ```

    -   or update a single image: `docker-compose pull plex`

-   Let compose update all containers as necessary:

     

    ```
    docker-compose up -d
    ```

    -   or update a single container: `docker-compose up -d plex`

-   You can also remove the old dangling images: `docker image prune`

### Via Docker Run

-   Update the image: `docker pull lscr.io/linuxserver/plex:latest`
-   Stop the running container: `docker stop plex`
-   Delete the container: `docker rm plex`
-   Recreate a new container with the same docker run parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)
-   You can also remove the old dangling images: `docker image prune`

### Via Watchtower auto-updater (only use if you don't remember the original parameters)

-   Pull the latest image at its tag and replace it with the same env variables in one run:

    ```bash
    docker run --rm \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower \
    --run-once plex
    ```

-   You can also remove the old dangling images: `docker image prune`

**Note:** We do not endorse the use of Watchtower as a solution to automated updates of existing Docker containers. In fact we generally discourage automated updates. However, this is a useful tool for one-time manual updates of containers where you have forgotten the original parameters. In the long term, we highly recommend using [Docker Compose](https://docs.linuxserver.io/general/docker-compose).

### Image Update Notifications - Diun (Docker Image Update Notifier)

-   We recommend [Diun](https://crazymax.dev/diun/) for update notifications. Other tools that automatically update containers unattended are not recommended or supported.

## Building locally

If you want to make local modifications to these images for development purposes or just to customize the logic:

```bash
git clone https://github.com/linuxserver/docker-plex.git
cd docker-plex
docker build \
  --no-cache \
  --pull \
  -t lscr.io/linuxserver/plex:latest .
```

The ARM variants can be built on x86_64 hardware using `multiarch/qemu-user-static`

```bash
docker run --rm --privileged multiarch/qemu-user-static:register --reset
```

Once registered you can define the dockerfile to use with `-f Dockerfile.aarch64`.



# docker-comppse

```bash
version: "2.1"
services:
  plex:
    image: lscr.io/linuxserver/plex:latest
    container_name: plex
    network_mode: bridge
    environment:
      - PUID=0
      - PGID=0
      - TZ=Asia/Shanghai
      - VERSION=docker
      - PLEX_CLAIM=claim-e7VQPPnccz9d8WjD8nzg
    ports:
      - 32400:32400
      - 1900:1900/udp
      - 3005:3005
      - 5354:5353/udp
      - 8324:8324
      - 32410:32410/udp
      - 32412:32412/udp
      - 32413:32413/udp
      - 32414:32414/udp
      - 32469:32469
    volumes:
      - ${PWD}/config:/config
      - /usr/docker_data/aria2/aria2-downloads:/tv
#      - /path/to/movies:/movies
    restart: unless-stopped
```