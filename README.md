[![Blog](https://img.shields.io/static/v1.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=ryanvanmassenhoven.com&message=Blog)](https://www.ryanvanmassenhoven.com "all the things you can do with our containers including How-To guides, opinions and much more!")
[![GitHub](https://img.shields.io/static/v1.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=ryanvanmass&message=GitHub&logo=github)](https://github.com/ryanvanmass "view the source for all of our repositories.")

The [LinuxServer.io](https://linuxserver.io) team previously maintained this image but recently depricated it in favour of a different implementation. I have choosen to fork there Repo and continue maintaining it.

Find me at:

* [Blog](https://www.ryanvanmassenhoven.com) - all the things you can do with our containers including How-To guides, opinions and much more!
* [GitHub](https://github.com/ryanvanmass) - view the source for all of our repositories.

[![GitHub Stars](https://img.shields.io/github/stars/ryanvanmass/docker-unifi-controller.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/ryanvanmass/docker-unifi-controller)
[![GitHub Release](https://img.shields.io/github/release/ryanvanmass/docker-unifi-controller.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/ryanvanmass/docker-unifi-controller/releases)
[![Docker Pulls](https://img.shields.io/docker/pulls/ryanvanmass/unifi-controller.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=pulls&logo=docker)](https://hub.docker.com/r/ryanvanmass/unifi-controller)
[![Docker Stars](https://img.shields.io/docker/stars/ryanvanmass/unifi-controller.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=stars&logo=docker)](https://hub.docker.com/r/ryanvanmass/unifi-controller)

The [Unifi-controller](https://www.ubnt.com/enterprise/#unifi) software is a powerful, enterprise wireless software engine ideal for high-density client deployments requiring low latency and high uptime performance.

[![unifi-controller](https://raw.githubusercontent.com/linuxserver/docker-templates/master/linuxserver.io/img/unifi-banner.png)](https://www.ubnt.com/enterprise/#unifi)

## Supported Architectures

We utilise the docker manifest for multi-platform awareness. More information is available from docker [here](https://distribution.github.io/distribution/spec/manifest-v2-2/#manifest-list) and our announcement [here](https://blog.linuxserver.io/2019/02/21/the-lsio-pipeline-project/).

Simply pulling `ryanvanmass/unifi-controller:latest` should retrieve the correct image for your arch, but you can also pull specific arch images via tags.

The architectures supported by this image are:

| Architecture | Available | Tag |
| :----: | :----: | ---- |
| x86-64 | ✅ | amd64-\<version tag\> |
| arm64 | ❌ | arm64v8-\<version tag\> |
| armhf | ❌ | |

## Version Tags

This image provides various versions that are available via tags. Please read the descriptions carefully and exercise caution when using unstable or development tags.

| Tag | Available | Description |
| :----: | :----: |--- |
| latest | ✅ | Stable Unifi Controller releases. |
| 8.5.6 | ✅ | Unifi Controller Version 8.0.26 |

## Application Setup


The webui is at https://ip:8443, setup with the first run wizard.

For Unifi to adopt other devices, e.g. an Access Point, it is required to change the inform IP address. Because Unifi runs inside Docker by default it uses an IP address not accessible by other devices. To change this go to Settings > System > Advanced and set the Inform Host to a hostname or IP address accessible by your devices. Additionally the checkbox "Override" has to be checked, so that devices can connect to the controller during adoption (devices use the inform-endpoint during adoption).

**Please note, Unifi change the location of this option every few releases so if it's not where it says, search for "Inform" or "Inform Host" in the settings.**

In order to manually adopt a device take these steps:

```
ssh ubnt@$AP-IP
set-inform http://$address:8080/inform
```

The default device password is `ubnt`. `$address` is the IP address of the host you are running this container on and `$AP-IP` is the Access Point IP address.

When using a Security Gateway (router) it could be that network connected devices are unable to obtain an ip address. This can be fixed by setting "DHCP Gateway IP", under Settings > Networks > network_name, to a correct (and accessable) ip address.

### Strict reverse proxies

This image uses a self-signed certificate by default. This naturally means the scheme is `https`.
If you are using a reverse proxy which validates certificates, you need to [disable this check for the container](https://docs.linuxserver.io/faq#strict-proxy).

## Usage

To help you get started creating a container from this image you can either use docker-compose or the docker cli.

### docker-compose (recommended, [click here for more info](https://docs.linuxserver.io/general/docker-compose))

```yaml
---
version: "2.1"
services:
  unifi-controller:
    image: ryanvanmass/unifi-controller:latest
    container_name: unifi-controller
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - MEM_LIMIT=1024 #optional
      - MEM_STARTUP=1024 #optional
    volumes:
      - /path/to/data:/config
    ports:
      - 8443:8443
      - 3478:3478/udp
      - 10001:10001/udp
      - 8080:8080
      - 1900:1900/udp #optional
      - 8843:8843 #optional
      - 8880:8880 #optional
      - 6789:6789 #optional
      - 5514:5514/udp #optional
    restart: unless-stopped
```

### docker cli ([click here for more info](https://docs.docker.com/engine/reference/commandline/cli/))

```bash
docker run -d \
  --name=unifi-controller \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Etc/UTC \
  -e MEM_LIMIT=1024 `#optional` \
  -e MEM_STARTUP=1024 `#optional` \
  -p 8443:8443 \
  -p 3478:3478/udp \
  -p 10001:10001/udp \
  -p 8080:8080 \
  -p 1900:1900/udp `#optional` \
  -p 8843:8843 `#optional` \
  -p 8880:8880 `#optional` \
  -p 6789:6789 `#optional` \
  -p 5514:5514/udp `#optional` \
  -v /path/to/data:/config \
  --restart unless-stopped \
  ryanvanmass/unifi-controller:latest
```

## Parameters

Containers are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 8443` | Unifi web admin port |
| `-p 3478/udp` | Unifi STUN port |
| `-p 10001/udp` | Required for AP discovery |
| `-p 8080` | Required for device communication |
| `-p 1900/udp` | Required for `Make controller discoverable on L2 network` option |
| `-p 8843` | Unifi guest portal HTTPS redirect port |
| `-p 8880` | Unifi guest portal HTTP redirect port |
| `-p 6789` | For mobile throughput test |
| `-p 5514/udp` | Remote syslog port |
| `-e PUID=1000` | for UserID - see below for explanation |
| `-e PGID=1000` | for GroupID - see below for explanation |
| `-e TZ=Etc/UTC` | specify a timezone to use, see this [list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List). |
| `-e MEM_LIMIT=1024` | Optionally change the Java memory limit (in Megabytes). Set to `default` to reset to default |
| `-e MEM_STARTUP=1024` | Optionally change the Java initial/minimum memory (in Megabytes). Set to `default` to reset to default |
| `-v /config` | All Unifi data stored here |

## Environment variables from files (Docker secrets)

You can set any environment variable from a file by using a special prepend `FILE__`.

As an example:

```bash
-e FILE__MYVAR=/run/secrets/mysecretvariable
```

Will set the environment variable `MYVAR` based on the contents of the `/run/secrets/mysecretvariable` file.

## Umask for running applications

For all of our images we provide the ability to override the default umask settings for services started within the containers using the optional `-e UMASK=022` setting.
Keep in mind umask is not chmod it subtracts from permissions based on it's value it does not add. Please read up [here](https://en.wikipedia.org/wiki/Umask) before asking for support.

## User / Group Identifiers

When using volumes (`-v` flags), permissions issues can arise between the host OS and the container, we avoid this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1000` and `PGID=1000`, to find yours use `id your_user` as below:

```bash
id your_user
```

Example output:

```text
uid=1000(your_user) gid=1000(your_user) groups=1000(your_user)
```

## Docker Mods

[![Docker Mods](https://img.shields.io/badge/dynamic/yaml?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=unifi-controller&query=%24.mods%5B%27unifi-controller%27%5D.mod_count&url=https%3A%2F%2Fraw.githubusercontent.com%2Flinuxserver%2Fdocker-mods%2Fmaster%2Fmod-list.yml)](https://mods.linuxserver.io/?mod=unifi-controller "view available mods for this container.") [![Docker Universal Mods](https://img.shields.io/badge/dynamic/yaml?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=universal&query=%24.mods%5B%27universal%27%5D.mod_count&url=https%3A%2F%2Fraw.githubusercontent.com%2Flinuxserver%2Fdocker-mods%2Fmaster%2Fmod-list.yml)](https://mods.linuxserver.io/?mod=universal "view available universal mods.")

We publish various [Docker Mods](https://github.com/linuxserver/docker-mods) to enable additional functionality within the containers. The list of Mods available for this image (if any) as well as universal mods that can be applied to any one of our images can be accessed via the dynamic badges above.

## Support Info

* Shell access whilst the container is running:

    ```bash
    docker exec -it unifi-controller /bin/bash
    ```

* To monitor the logs of the container in realtime:

    ```bash
    docker logs -f unifi-controller
    ```

* Container version number:

    ```bash
    docker inspect -f '{{ index .Config.Labels "build_version" }}' unifi-controller
    ```

* Image version number:

    ```bash
    docker inspect -f '{{ index .Config.Labels "build_version" }}' lscr.io/linuxserver/unifi-controller:latest
    ```

## Updating Info

Most of our images are static, versioned, and require an image update and container recreation to update the app inside. With some exceptions (ie. nextcloud, plex), we do not recommend or support updating apps inside the container. Please consult the [Application Setup](#application-setup) section above to see if it is recommended for the image.

Below are the instructions for updating containers:

### Via Docker Compose

* Update images:
    * All images:

        ```bash
        docker-compose pull
        ```

    * Single image:

        ```bash
        docker-compose pull unifi-controller
        ```

* Update containers:
    * All containers:

        ```bash
        docker-compose up -d
        ```

    * Single container:

        ```bash
        docker-compose up -d unifi-controller
        ```

* You can also remove the old dangling images:

    ```bash
    docker image prune
    ```

### Via Docker Run

* Update the image:

    ```bash
    docker pull lscr.io/linuxserver/unifi-controller:latest
    ```

* Stop the running container:

    ```bash
    docker stop unifi-controller
    ```

* Delete the container:

    ```bash
    docker rm unifi-controller
    ```

* Recreate a new container with the same docker run parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)
* You can also remove the old dangling images:

    ```bash
    docker image prune
    ```

### Via Watchtower auto-updater (only use if you don't remember the original parameters)

* Pull the latest image at its tag and replace it with the same env variables in one run:

    ```bash
    docker run --rm \
      -v /var/run/docker.sock:/var/run/docker.sock \
      containrrr/watchtower \
      --run-once unifi-controller
    ```

* You can also remove the old dangling images: `docker image prune`

**warning**: We do not endorse the use of Watchtower as a solution to automated updates of existing Docker containers. In fact we generally discourage automated updates. However, this is a useful tool for one-time manual updates of containers where you have forgotten the original parameters. In the long term, we highly recommend using [Docker Compose](https://docs.linuxserver.io/general/docker-compose).

### Image Update Notifications - Diun (Docker Image Update Notifier)

**tip**: We recommend [Diun](https://crazymax.dev/diun/) for update notifications. Other tools that automatically update containers unattended are not recommended or supported.

## Building locally

If you want to make local modifications to these images for development purposes or just to customize the logic:

```bash
git clone https://github.com/ryanvanmass/docker-unifi-controller.git
cd docker-unifi-controller
docker build \
  --no-cache \
  --pull \
  -t lscr.io/linuxserver/unifi-controller:latest .
```

The ARM variants can be built on x86_64 hardware using `multiarch/qemu-user-static`

```bash
docker run --rm --privileged multiarch/qemu-user-static:register --reset
```

Once registered you can define the dockerfile to use with `-f Dockerfile.aarch64`.

## Versions
* **03.10.24:** - Picked Up by RyanVanMass
* **01.01.24:** - Deprecate.
* **05.09.23:** - Add deprecation warning as per [https://info.linuxserver.io/issues/2023-09-06-unifi-controller](https://info.linuxserver.io/issues/2023-09-06-unifi-controller).
* **04.09.23:** - Bump JRE to 17 to support v7.5.
* **02.05.23:** - Cleanup `apt-get install` during build to reduce image size.
* **18.03.23:** - Add mongoless branch.
* **10.03.23:** - Test writing to /run/unifi and symlink to /config/run if it fails.
* **20.02.23:** - Migrate to s6v3, install deb package on build, fix permissions.
* **23.01.23:** - Exclude `run` from `/config` volume.
* **30.11.22:** - Bump JRE to 11.
* **01.06.22:** - Deprecate armhf.
* **23.12.21:** - Move min/max memory config from run to system.properties.
* **22.12.21:** - Move deb package install to first init to avoid overlayfs performance issues.
* **13.12.21:** - Rebase 64 bit containers to Focal.
* **11.12.21:** - Add java opts to mitigate CVE-2021-44228.
* **11.06.21:** - Allow for changing Java initial mem via new optional environment variable.
* **12.01.21:** - Deprecate the `LTS` tag as Unifi no longer releases LTS stable builds. Existing users can switch to the `latest` tag. Direct upgrade from 5.6.42 (LTS) to 6.0.42 (latest) tested successfully.
* **17.07.20:** - Rebase 64 bit containers to Bionic and Mongo 3.6.
* **16.06.20:** - Add logrotate.
* **02.06.20:** - Updated port list & descriptions. Moved some ports to optional.
* **14.11.19:** - Changed url for deb package to match new Ubiquity domain.
* **29.07.19:** - Allow for changing Java mem limit via new optional environment variable.
* **23.03.19:** - Switching to new Base images, shift to arm32v7 tag.
* **10.02.19:** - Initial release of new unifi-controller image with new tags and pipeline logic
