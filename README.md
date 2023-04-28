<!-- DO NOT EDIT THIS FILE MANUALLY  -->

# [imagegenius/semaphore](https://github.com/imagegenius/docker-semaphore)

[![GitHub Release](https://img.shields.io/github/release/imagegenius/docker-semaphore.svg?color=007EC6&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/imagegenius/docker-semaphore/releases)
[![GitHub Package Repository](https://shields.io/badge/GitHub%20Package-blue?logo=github&logoColor=ffffff&style=for-the-badge)](https://github.com/imagegenius/docker-semaphore/packages)
[![Jenkins Build](https://img.shields.io/jenkins/build?labelColor=555555&logoColor=ffffff&style=for-the-badge&jobUrl=https%3A%2F%2Fci.imagegenius.io%2Fjob%2FDocker-Pipeline-Builders%2Fjob%2Fdocker-semaphore%2Fjob%2Fmain%2F&logo=jenkins)](https://ci.imagegenius.io/job/Docker-Pipeline-Builders/job/docker-semaphore/job/main/)
[![IG CI](https://img.shields.io/badge/dynamic/yaml?color=007EC6&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=CI&query=CI&url=https%3A%2F%2Fci-tests.imagegenius.io%2Fsemaphore%2Flatest-main%2Fci-status.yml)](https://ci-tests.imagegenius.io/semaphore/latest-main/index.html)

[Semaphore](https://ansible-semaphore.com/) is a modern UI for Ansible. It lets you easily run Ansible playbooks, get notifications about fails, control access to deployment system.

[![semaphore](https://raw.githubusercontent.com/imagegenius/templates/main/unraid/img/semaphore.png)](https://ansible-semaphore.com/)

## Supported Architectures

We use Docker manifest for cross-platform compatibility. More details can be found on [Docker's website](https://github.com/docker/distribution/blob/master/docs/spec/manifest-v2-2.md#manifest-list).

To obtain the appropriate image for your architecture, simply pull `ghcr.io/imagegenius/semaphore:latest`. Alternatively, you can also obtain specific architecture images by using tags.

This image supports the following architectures:

| Architecture | Available | Tag |
| :----: | :----: | ---- |
| x86-64 | ✅ | amd64-\<version tag\> |
| arm64 | ✅ | arm64v8-\<version tag\> |
| armhf | ❌ | |

## Application Setup

The WebUI can be accessed at `http://your-ip:3000`, login with the admin user/password set in the variables.

The home for Semaphore (`abc`) is set to `/config`, so that SSH keys can be stored in `/config/.ssh`.

To get SSH working, you first need to SSH into the client (with keys of course). Add your key to `/config/.ssh/id_rsa`, make sure that you run `chmod 600 id_rsa` and `chown abc:abc id_rsa`.

You can now run `s6-setuidgid abc /bin/bash` to open a shell as `abc`, and run your SSH commands (`ssh <user>@<host>`), and you should be prompted with `yes`/`no`, type `yes`, then exit the SSH session, Semaphore/Ansible should now be able to SSH into that client.

## Usage

Example snippets to start creating a container:

### Docker Compose

```yaml
---
version: "2.1"
services:
  semaphore:
    image: ghcr.io/imagegenius/semaphore:latest
    container_name: semaphore
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - SEMAPHORE_DB_DIALECT=bolt
      - SEMAPHORE_ADMIN=admin
      - SEMAPHORE_ADMIN_PASSWORD=password
      - "SEMAPHORE_ADMIN_NAME=John Doe"
      - SEMAPHORE_ADMIN_EMAIL=example@me.com
      - SEMAPHORE_ACCESS_KEY_ENCRYPTION=admin
      - SEMAPHORE_DB_HOST=192.168.1.x #optional
      - SEMAPHORE_DB_PORT= #optional
      - SEMAPHORE_DB_USER=semaphore #optional
      - SEMAPHORE_DB_PASS=semaphore #optional
      - SEMAPHORE_DB=semaphore #optional
    volumes:
      - </path/to/appdata>:/config
    ports:
      - 3000:3000
    restart: unless-stopped
```

### Docker CLI ([Click here for more info](https://docs.docker.com/engine/reference/commandline/cli/))

```bash
docker run -d \
  --name=semaphore \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Etc/UTC \
  -e SEMAPHORE_DB_DIALECT=bolt \
  -e SEMAPHORE_ADMIN=admin \
  -e SEMAPHORE_ADMIN_PASSWORD=password \
  -e SEMAPHORE_ADMIN_NAME="John Doe" \
  -e SEMAPHORE_ADMIN_EMAIL=example@me.com \
  -e SEMAPHORE_ACCESS_KEY_ENCRYPTION=admin \
  -e SEMAPHORE_DB_HOST=192.168.1.x `#optional` \
  -e SEMAPHORE_DB_PORT= `#optional` \
  -e SEMAPHORE_DB_USER=semaphore `#optional` \
  -e SEMAPHORE_DB_PASS=semaphore `#optional` \
  -e SEMAPHORE_DB=semaphore `#optional` \
  -p 3000:3000 \
  -v </path/to/appdata>:/config \
  --restart unless-stopped \
  ghcr.io/imagegenius/semaphore:latest

```

## Variables

To configure the container, pass variables at runtime using the format `<external>:<internal>`. For instance, `-p 8080:80` exposes port `80` inside the container, making it accessible outside the container via the host's IP on port `8080`.

| Variable | Description |
| :----: | --- |
| `-p 3000` | WebUI Port |
| `-e PUID=1000` | UID for permissions - see below for explanation |
| `-e PGID=1000` | GID for permissions - see below for explanation |
| `-e TZ=Etc/UTC` | Specify a timezone to use, see this [list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List). |
| `-e SEMAPHORE_DB_DIALECT=bolt` | Choose either `bolt`, `postgres` or `mysql`. If `bolt` is chosen, the optional variables (`SEMAPHORE_DB...`) do not need to be specified as `bolt` is a 'built-in' database |
| `-e SEMAPHORE_ADMIN=admin` | Specify the admin user |
| `-e SEMAPHORE_ADMIN_PASSWORD=password` | Specify the admin password |
| `-e SEMAPHORE_ADMIN_NAME=John Doe` | Specify the admin name |
| `-e SEMAPHORE_ADMIN_EMAIL=example@me.com` | Specify the admin email |
| `-e SEMAPHORE_ACCESS_KEY_ENCRYPTION=admin` | Specify the key for encrypting access keys in database. It must be generated by using the following command: `head -c32 /dev/urandom | base64`. |
| `-e SEMAPHORE_DB_HOST=192.168.1.x` | Host IP of PostgreSQL or MySQL |
| `-e SEMAPHORE_DB_PORT=` | Port to connect to PostgreSQL (`5432`) or MySQL (`3306`) |
| `-e SEMAPHORE_DB_USER=semaphore` | PostgreSQL/MySQL database user |
| `-e SEMAPHORE_DB_PASS=semaphore` | PostgreSQL/MySQL database password |
| `-e SEMAPHORE_DB=semaphore` | PostgreSQL/MySQL database |
| `-v /config` | Appdata Path |

## Umask for running applications

All of our images allow overriding the default umask setting for services started within the containers using the optional -e UMASK=022 option. Note that umask works differently than chmod and subtracts permissions based on its value, not adding. For more information, please refer to the Wikipedia article on umask [here](https://en.wikipedia.org/wiki/Umask).

## User / Group Identifiers

To avoid permissions issues when using volumes (`-v` flags) between the host OS and the container, you can specify the user (`PUID`) and group (`PGID`). Make sure that the volume directories on the host are owned by the same user you specify, and the issues will disappear.

Example: `PUID=1000` and `PGID=1000`. To find your PUID and PGID, run `id user`.

```bash
  $ id username
    uid=1000(dockeruser) gid=1000(dockergroup) groups=1000(dockergroup)
```

## Updating the Container

Most of our images are static, versioned, and require an image update and container recreation to update the app. We do not recommend or support updating apps inside the container. Check the [Application Setup](#application-setup) section for recommendations for the specific image.

Instructions for updating containers:

### Via Docker Compose

* Update all images: `docker-compose pull`
  * or update a single image: `docker-compose pull semaphore`
* Let compose update all containers as necessary: `docker-compose up -d`
  * or update a single container: `docker-compose up -d semaphore`
* You can also remove the old dangling images: `docker image prune`

### Via Docker Run

* Update the image: `docker pull ghcr.io/imagegenius/semaphore:latest`
* Stop the running container: `docker stop semaphore`
* Delete the container: `docker rm semaphore`
* Recreate a new container with the same docker run parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)
* You can also remove the old dangling images: `docker image prune`

## Versions

* **28.04.23:** - Added rsync package.
* **14.04.23:** - Initial release.
