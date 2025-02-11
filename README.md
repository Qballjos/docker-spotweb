# Dockerfile to set up Spotweb on x86 and arm based systems

The main goal of this Dockerfile is to easily set up Spotweb using Docker on the Raspberry Pi (or any compatible arm chipset) and regular x86 chipsets.

## Quick setup

- The recommended way to use this Docker image is to use the pre-build image that is available on Docker Hub
- Visit [Spotweb on Docker Hub](https://hub.docker.com/repository/docker/qballjos/spotweb)
- The pre-build image works on multiple CPU architectures (x86, arm64 and armv7)

## Quick setup using dockerfile

_Spotweb always requires a database server (e.g. MySQL), the easiest solution is to use the docker-compose setup. The other option is to manually specify an external server using the ENV variables below._

`docker run -p 8085:80 --name spotweb -d -v /etc/localtime:/etc/localtime:ro qballjos/spotweb`

Provide one or more of the following environment variables to configure the database server (all optional, default values are given below):

- TZ (default = `Europe/Amsterdam`)
- DB_ENGINE (default = `pdo_mysql`)
- DB_HOST (default = `mysql`)
- DB_PORT (default = `3306`)
- DB_NAME (default = `spotweb`)
- DB_USER (default = `spotweb`)
- DB_PASS (default = `spotweb`)

E.g. to configure MySQL database server with host `some.external.mysql-server.com`, port `6612` and timezone `Europe/London` do the following:

`docker run -p 8085:80 --name spotweb -d -v /etc/localtime:/etc/localtime:ro -e "DB_HOST=some.external.mysql-server.com" -e "DB_PORT=6612" qballjos/spotweb`

Instead of using the pre-build image on Docker Hub you can build the image locally, for example:

```
docker build -t spotweb .

docker run -p 8085:80 \
  --name spotweb -d \
  -e DB_HOST=some.external.mysql-server.com \
  -e DB_PORT=6612 \
  -e TZ=Europe/London \
  spotweb
```

## Quick setup using Docker Compose

Included are example Docker Compose files, modify them as desired (e.g. change the MySQL root password).

- `docker compose up` (to use pre-build image from Docker Hub) or `docker compose -f docker-compose-local.yml up` (to build the image locally)

## Configure Spotweb

- Visit `http://localhost:8085`
- Login with username `admin` and password `spotweb`
- Configure usenet server, spot retention, etc. and wait for spots to appear (retrieval script runs once every 5 minutes)

## Tip: Using `ownsettings.php`

You can override Spotweb settings by using a custom `ownsettings.php` file. In most cases there is no need to use this feature, so only use this when you know what you are doing!

The example below will mount `/external/ownsettings.php` to `/app/ownsettings.php` inside the container. Spotweb will see the ownsettings file and load it automatically.

```
volumes:
- /external/ownsettings.php:/app/ownsettings.php
```

## Information

- In the past separate images were created for x86 and arm in separate Docker Hub repositories, for the most up to date image please visit [Docker Hub](https://hub.docker.com/repository/docker/qballjos/spotweb)
- :warning: Spotweb is configured as an open system after running docker compose up, so everyone who can access the site can register an account (keep this in mind, and also make sure to change the admin password if you plan to expose Spotweb to the outside world!) :warning:
- If you want to use the Spotweb API, create a new user and use the API key associated with that user
- See the [official Spotweb Wiki](https://github.com/spotweb/spotweb/wiki) for any questions regarding Spotweb
